# CD-KEY

Category: Misc (though its really more like rev deobfuscation + web php)

we are provided a url: https://cd-key.ctf.bsidestlv.com/ and it allows us to enter a key to activate
we are given an [`index.php`](./index.php) file, of which there is a obfuscated php code to validate the key:
```php
<?php

function validate_key($Nd2Ei) : bool { goto EHUvl; EHUvl: $RswYn = "\57\136\133\60\x2d\71\135\x7b\65\x7d\x2d\x5b\x41\x2d\132\141\x2d\x7a\x5d\x7b\63\x7d\55\133\60\x2d\x39\135\173\67\x7d\55\x5b\60\x2d\x39\135\173\65\175\x24\x2f\151"; goto Jqlvn; IiZFB: return $iemVT % 7 == 0; goto To7Hm; tI8cc: $Nd2Ei = htmlspecialchars($Nd2Ei); goto LQHHH; aZ2HL: $iemVT = 0; goto Jsi21; Y1JYP: if (is_numeric($Nd2Ei[$mM5aW + 18])) { goto BcHCu; } goto lIceC; va6be: eHqaj: goto c9E41; Q6G_X: if (in_array(substr($Nd2Ei, 3, 2), [$MN1tG[52] . $MN1tG[52], $MN1tG[52] . $MN1tG[53], $MN1tG[52] . $MN1tG[54], $MN1tG[61] . $MN1tG[57], $MN1tG[61] . $MN1tG[58], $MN1tG[61] . $MN1tG[59], $MN1tG[61] . $MN1tG[60], $MN1tG[61] . $MN1tG[61]])) { goto rozEh; } goto jjH1i; Jxyt_: return false; goto RCR3Q; jjH1i: return false; goto tJ7SK; LEG2h: return false; goto F2lTN; m0zed: BcHCu: goto HFfrL; F2lTN: chlBQ: goto L7XME; lIceC: return false; goto m0zed; p9Isd: goto eHqaj; goto Jj9rm; Jsi21: $mM5aW = 10; goto va6be; E1z1K: if (!(strtoupper(substr($Nd2Ei, 6, 3)) != $MN1tG[14] . $MN1tG[4] . $MN1tG[12])) { goto cda8y; } goto BcOlL; Jj9rm: KPRmF: goto IiZFB; wypRV: mzemE: goto diCkR; vF84E: $mM5aW++; goto p9Isd; c9E41: if (!($mM5aW < 17)) { goto KPRmF; } goto EhUBF; tDhR4: if (preg_match($RswYn, $Nd2Ei)) { goto chlBQ; } goto LEG2h; UjYJ3: goto mzemE; goto XdkLx; Jqlvn: $MN1tG = array_merge(range("\101", "\x5a"), range("\141", "\x7a"), range(0, 9), str_split("\41\x40\43\44\45\136\46\52\50\x29\137\x2b\x2d\75\133\x5d\173\x7d\174\73\x3a\x2c\56\x3c\76\77")); goto tDhR4; tJ7SK: rozEh: goto E1z1K; Hf6ou: $mM5aW++; goto UjYJ3; L7XME: $Nd2Ei = trim($Nd2Ei); goto jKwmW; RCR3Q: AiYjL: goto Q6G_X; HFfrL: h5pal: goto Hf6ou; IgbLP: $mM5aW = 0; goto wypRV; eA3ZV: cda8y: goto IgbLP; EhUBF: $iemVT += $Nd2Ei[$mM5aW]; goto BUf3P; diCkR: if (!($mM5aW < 5)) { goto xB2El; } goto Y1JYP; XdkLx: xB2El: goto aZ2HL; jKwmW: $Nd2Ei = stripcslashes($Nd2Ei); goto tI8cc; BcOlL: return false; goto eA3ZV; LQHHH: if (!((int) substr($Nd2Ei, 0, 3) > (int) $MN1tG[55] . $MN1tG[58] . $MN1tG[58])) { goto AiYjL; } goto Jxyt_; BUf3P: O85xy: goto vF84E; To7Hm: }

?>
```

Before we go into deobfuscating the code, a few things:

when php does `goto a;`, they simply mean go to the `a` marker, and continue executing, ignoring other markers. So for example:
```php
goto a;
echo "Yuyuko Saigyouji\n";
a:
echo "Perfect Ink-Black Cherry Blossom\n";
b:
echo "Resurrection Butterfly\n";
```
prints:
```
Perfect Ink-Black Cherry Blossom
Resurrection Butterfly
```

https://onlinephp.io/ was also very useful for verifying that my deobfuscation was correct.

We then start jumping into the rabbit hole, following all the `goto` statements that lead into other `goto` statements in a wild goose chase (manually). Eventually, we reach code like this:
```php
$regex = "/^[0-9]{5}-[A-Za-z]{3}-[0-9]{7}-[0-9]{5}$/i"; 
$tokens = array_merge(range("A", "Z"), range("a", "z"), range(0, 9), str_split("!@#$%^&*()_+-=[]{}|;:,.<>?")); 
if (preg_match($regex, key)) { 
	key = trim(key); 
	key = stripcslashes(key); 
	key = htmlspecialchars(key); 
	if ((int) substr(key, 0, 3) <= 366 ) { 
		if (in_array(substr($key, 3, 2), ["00", "01", "02", "95", "96", "97", "98", "9945"]){ 
			if (strtoupper(substr($key, 6, 3)) == "OEM") { 
				$i = 0; 
				mzemE: 
				if (!($i < 5)){ 
					$n = 0; 
					$i = 10; 
					eHqaj: 
					if (!($i < 17)) { 
						return $n % 7 == 0; 
					} 
					$n += $key[$i]; 
					$i++; 
					goto eHqaj; 
				} if (is_numeric($key[$i + 18])) { 
					$i++; 
					goto mzemE; 
				} 
			}
		} 
	}
}
```

we can sort of arrange the inner code into a loop:
```php
$regex = "/^[0-9]{5}-[A-Za-z]{3}-[0-9]{7}-[0-9]{5}$/i"; 
$tokens = array_merge(range("A", "Z"), range("a", "z"), range(0, 9), str_split("!@#$%^&*()_+-=[]{}|;:,.<>?")); 
if (preg_match($regex, key)) { 
	key = trim(key); 
	key = stripcslashes(key); 
	key = htmlspecialchars(key); 
	if ((int) substr(key, 0, 3) <= 366 ) { 
		if (in_array(substr($key, 3, 2), ["00", "01", "02", "95", "96", "97", "98", "9945"]){ 
			if (strtoupper(substr($key, 6, 3)) == "OEM") { 
				$i = 0; 
				while ($i < 5){
					if (is_numeric($key[$i + 18])) { 
						$i++; 
					} else {
						return false;
					}
				}
				$n = 0;
				$i = 10;
				while (i < 17){
					$n += $key[$i];
					$i++;
				}
				return $n % 7 == 0;
			}
			return false;
		}
		return false;
	}
	return false;
}
return false;
```

The regex forces the key format to be `<5 digits>-<3 chars>-<7 digits>-<5 digits>`
The next check is that first three digits must be (when treated as an integer) be less than 366
The next two digits must then be one of these: `[00, 01, 02, 95, 96, 97, 98]` 
The next three chars must be `"OEM"`, either lower or uppercase
The next seven digits must add up to be a multiple of 7
The last five digits simple have to be numeric.
A key I came up with is `36600-OEM-9570000-11111`
Upon activation, we get the flag: `BSidesTLV2023{WiN95_Pr3d1ct4bl3_AlG0R1ThM}`