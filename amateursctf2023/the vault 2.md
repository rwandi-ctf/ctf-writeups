# The Vault 2

category: crypto

author: `hellopir2`

> I remember this cool HSCTF-22 challenge that gave you like an admin key or something and then made you find bigger solutions. Well this time I'm not giving you an admin key. Good luck!

We are given a `main.py`:

```py
#!/usr/local/bin/python
from flag import flag
import sys

sys.set_int_max_str_digits(0)

a = input("a: ")
b = input("b: ")
c = input("c: ")
x = input("x: ")
y = input("y: ")
z = input("z: ")


try:
    a, b, c, x, y, z = list(map(int, [a,b,c,x,y,z]))
except:
    print("Invalid input")
    exit(0)

for i in [a,b,c,x,y,z]:
    if i <= 10**3000:
        print("Key not long enough!")
        exit(0)

if a**3 + 12*a**2*c + 48*a*c**2 - b**3 - 18*b**2*c - 108*b*c**2 - 241*c**3 + 3*c**2*x + 12*c**2*y - 48*c**2*z + 3*c*x**2 + 6*c*y**2 - 6*c*z**2 + x**3 + y**3 == 0x13e0bf624acc9f90dd8e740c34bd748f03322b63836aec98a2cef943a45718fc621806cbdf97932330e01850196544907ccee0d4ade048fc2e3c4bc966b4069174ef4154183d48317cf4356cbd74640bd9638413801a03b9ec5cbe180438852c88c01321fd6866f5dc40f7a494aef2822d3b7ba3223c8d71d553ef0fb6ec584ceb3ba3a7901e6a5ed124c9192696884cc3c88a31478debafbf42eed9f42b4c54840f543637868074084e2d710d0a2d5e195285973be7259186971a5e869cfb39ace5ea0d082257db88ccd9f21c968faad95ad8b40dfa05a5d8ad8ee8e8b15775406a2cb1814f5c0265e3da2f83126d7e429d1712e5b64e12c947372857d3525a75b77e4fb61a50d3d6b531fb933c24e71c20376ff78327ba183274986589e1b44fe90b8507a4c017b78d5ec29b9a45681124846b249927696daec438150e3bcca1212e1c21104d47764970e56472261494a0299e680fefeb5bff761e90b29a461d0d7c91d40769f2189a5199dbc19ae4989a0c0e4b06d126eb3739217bef6bbf350cc2c1765e76acb3e60aedc6de3fda16769a18e8478f49988419997f8dad33829a1abb555699a44c6b995b2e994ea17e489d9af41839b189287a7e22f9ef1ffb2f6aa4fd8c78a78933e35d52f169d55cfb9b72bdfb0e1483562f08e9faa34cffac1604c4eb33c28f431c8e65a50730771a1a4e21fc6f2cfebecd3ac9a2e36c34b66eb4c00a7507a3279e0d3f897eb1778e7114576c95267a61b50b8098adbcef8d804eda75916f3abe80882a1f74c7d65716e5971277f83685ee7b07e745ce9600f718f9022cffad957800a8686fd6b360c8661917ad708e14582c079ded90cd7d8f76a6f881f81fc16646f9a7eefacc0c482fb9a68de6491e96bafbb71eb3502a12a69aafe9afdc7f1d8a790d9d49b2b23200b0843219d369187693a25a6711916197e36e61bb470369ceedbe592beb5a4b9ad97066dc07eca061652830ef13cff8f9f460e8e530865f931803fc168a92684f4e501ea99215ea7ca7b540db74eb339afb4ac59f74052a8273b6269b00be3c81cd252b5a2cd71390054177de2c08aa07bca9f4c90d8942e01c7b797099e2e0ac265098de1fe464b22bea8e6f85a4b81b1faeb0f424965b23477cdd6a2395a587506e523943a8ce86c4caee5b1eb23c89328272b23ebe5576c48b3eda367ae4eb4fddabf89b948cf2fadeca232f915c06ecba91bd71e5b0fb1bfccbbc9a20161bccb247a83d9e2a0efbfdab15fd91a0f9a4ca17854026b584496f728f791344bebac17287e95523a25418994be66ffa53ccd353365713dd6a23b1c240130d7b831bcd5ca2f3cf08aebaa1f9c972182d0f8c14e4f9ca1a6036d78bebda08f574b3cc49d878323e5416823b80d184f5397552265e1a12b8d288d7d1822f125855873fdba3444ba02697da567227f1729d064f7e1aad159320e1fd9ec294612eca108f386af60fa33578e0b954967cef8c738c9782e25c4df3dd910b1dcab683073c2e5c3296b04d537d2b8bf50123a3ea84de70fb972c1207c071d7223f3201d4b892465211bd1687611f6139786954df66611c24a9bea7a2f3c37c43beddf5f3409ae6fe5507af9b94378f1a5636bd8d6e2b658c354d6dab44966bf4564180b6751ae446a559661383fd97a8522d130c5b6931e7720fb74ba0627e9c150f212dce9369cef249b6705b59dd1dbae4ca496f75b81cafe7b873dcd2eb5c00bc68cb8b164fb03dc956518fbc3a4c0598acc4b83022f93d09e8f15bf65da878b359eac776e75b0d9961bf5e7bef7a9a23b25b0564a77cb573e4e881102cf025adf5b963a41b57bd138c423a293e0bd1c9ad41c3cd0f0f9977b4770de9ca701ff151ba9946987d728fadc681f678c22f968e25d84ea04dceb2ea3b175cb1b52e700b816295778e654d4d6590c0e54e37f0109d034e760ca8cc8667e5be6a648c8df64aeb49445abc4660f0a43136a2baa6a2b85ac96a7eeed25a9703e0bc1f7c6d918c83e71c5c0c04242ecfe1548f9e4ee337db521879f60cf12bb44eeb43e9638d801eb5c59a30d049e4321f181ce46121fe224bfc9f923783c180dfd88112a929f2c172573b7ef96a1749591ebf94183e67473ef394f965d6fb9da9f4b034ec48b1f8ab5668cd7de889e7724d3bce7f0059a01f8a210e790cbf464428e52a48d04515053a9128f64163773a85d35056ab1ac695d5749e921b104a8e19bfa253349980debe3ee48ba858b18432c6ab0b6bb6fa7ee27f829ccc3cdf2aa5c0bff0a99ef9b0359e9e23ddd7ecd319d57134c390dce5703ee2610f0a6b74b01e7ff4f6739a1773574e8c77b5fb83d62abf6eac817684d60f117994b6c20d9592f8a19edf26d55c9e8e652f441546d2a023ed7b2e6b0461b18d22257e1ba13747901858d5c2fe53dbda128aad6c3fd70ee89baddf404f848be0dcf8c069696c10b85d55555f1197a3447bb70b6ca31b7eaac761f1b039144ff39f439e8e02c07112168a0e399ccca53b755adefac41dbc1c73dc81becb675fd9a975ad558a0d799c8d1ecd8250ccb9f9cf4e31169bc97b41bb3e0ccc3938fe5406a08a1b6b0c7f058f17109f1f2360bbb4ae8008b4390df4d8ff664f528e8d37b6fabab66a03a462fb5bd5a1adb89808353bea8c56b4c2ef991efc7041ce2393baea991b4d3453d968729b0174c084fbf3c00e2a8d917f3392bd9963f95a2c7e30a4824c75d3829caf080d1b37337f22dd473c8f8e52a5bb64b16f0615c3cfda6718975d459ee97a774743121eef4fc60f3dfe56b929cf65c62fa05c87cce16d23df336694cb2c929ea5b71cae5383b5b789b23da18ca8c6c7dd2adbf9e446fbda0305b6da35d2abe2ceec8e0ce07ef388aa75b9322445696a0ae94ccd30ccb6e35be23bb9dd667eaaf88cb5db6b5c35b0f300444137a450397cd846f9d564abf8ab6317ef0c27a829b8ce676dc86dc55195af8c8bb0e01d328a99fcd5ac30869a764c67a01d9bbb0aad353e53fef10a40b64504b8c1af12339c3ea2d221122dfb4dac6319a5a14bf70b27acedec02a0ff686cacc60b1ecffb22fc42821efe17b666685463a648931e54fee86da5dfe5e19084ab3dd5facf0ca956b2896536f179cf3be762f02475f375aaf84ac6202b04397ddfb6dd0e20dc7648fd2e08eb039b3cb90602ebcfdd92e4f82cd79dc6fd3659f90391b6191797888c8a7f5e651e419d6c50734b5d0a625c324fca5ae2102647ecceb2dbfc09b5731961bed080ee4c9b01a26807ec3b7aa6c924a07b1f9b7174cbf72d10307f5314cc2a2a71d8b1d1a7d11e0467b35bfc2b4f77ae4099acaa80333d6303dc9879765aa367d835efd5e49036653f2fa3ac30bb715fd41596ec529ec5b41d213213c6ef826b5ebb5d78b023296ed93279ecb447c55cab0d221d56e4526511f623003e87f5f1df739d68e85c5a03565bda9c722781ea787c78bc66e43ebba3670ad0d817691b8e7b9c0649231f755d676dc53c95eea31ec4dabc4ae0df465d4d0102deefdb21f1af6f50655e19035caee9a191ccdca7ded7f86cb5b3860be1828822825337739481cfa3851a9bee579be91ebc3951286a0c23069489a830b25a59913521148a66a851bb59a6165f5f4e245143c5aa936f3443e44e27f0dd9500e41d5a8bacb80488c33c337212a024937098241234e6a78e6e50ef0774951e6794b36ff5d5ae0fffb4148c9588980d2396c9cf7b0b09df7b8c78c40a428ee6df9e8881d968706e5e882a4aa0f3be899937645b57081998c38e34b82fa1b4f6e51f8dfece6448db2a35dadaced7c421ae77309b1010a7d9a7f8ce44b7f2b94e1d3e1615f3a7fa944becf7ec7d4418081f6491fc3d6f155b7a43466a520254b375bddf3a1ffdaadd75db4e1b6e3685096110cd7a99e9c6db2ac734c6c9751377a2fd90ff9b2f414fa0c70ff851bf77816eac84a450dfc37e29a0bc82759acf3090627614118d144bd59f538bf4e1682b202c32427829f00cb55952db60810c6df3a566eeeaebb16a5a3743fa99f7384e30fbb71a6075fca1ed9e47de129dc785a4820c189e0249e6bd9c4c45062c05f50a63f5d045678cea91cd6b3ba535692bcc94a80fa7db2dda1290a2ece5d094edf51762bddd3e87d267b66af9b086cd91a3bae39d3ee6bd4ca205c76220330b6816c1f2ceeff8f1e0a9071602bcdda0f3a81f864244894cf0f811a057a5173ec89281da79079d9f8ebea8cd91d2c9f15d59da3ef4d9ac69aa47412036a38ea981546d53e418993160fdb5f34f4285a22520aa3fe1636a3c5279dc92811d3259240638b1bd387ab96914ef483c1595bba2e9aaff308834b1831a84c504e501e8251afdec414a9cf162df59ed63c67949fc8c7e4bea2e8b89e57b8f47f78d493241b240c6c87afcbf02730945ee33d0535e8cf5d27f64aacc39309db11f27cd19dcc544d3db8d91db3a4a21a237651c3034b0abf93011ec768d6ffb88cf962078a4220796241828efc13e23de3a1d37940ee59eda641e9c1a053dee64840e57e5d1a68a696758e54ffc0ff47043f8b5b74faf86f23d84ba3df8a3a3f9649d611d02ff6f605ce95858927025050c4c40212fd2192261323ab3d52c5bd5c5246a77455eaedfbc34c66e78cedf44913e454a31b1418197c357e1c2e43af8b31f19e00aed31491f3eead506e69df4ff96fe04d79f42b9d339ac76c3e54b8e83fc1a95d2f5204fd3b11087d477a35707b4d6304696b9284e8ad5bcb3b9c0be5eeadeb37601a5e73a935f0924e256688c790e6e109ed6780d546369620f1201622ebeb6a54a4901ae5437565d74526730c3d4d128c9f7b84084b58bdf660cb430f6514f23ef6ffedd34de80a1269beecfca9be8c6a77a87ace7f209a0e8439fb18752cf35754b71a73c13d85cb26b38d99b7768f9a3049ec4182218eb069a8e79bf3c2690d495becd32a2924b781b85f1aff8f50c889da744a83cdc8fe7bd3f042a68ba0cb64e3d7b491e6e06cd3ce046bc04249e5a8a6fb06c22eb3532daf07d50510b1a55708c335bc09817f85d2a3faa1f9ca5923ff25d3ac8303a85b79aed12fd7763504a062d10804765f6b1919802ca92dc689d3942f3d8ea8b6204756467879f70e5c7cdefc9e9c69eacfa00bd6fb7b0720f1f4d01b01b564cdff31589074e4c4c8ecce5ba14f44b11ab617730956be72845921b208f0836f2ddf8c2bad181cadeb78cfa390ccfbfe8c0a587b8e9c6eddaac92a42bf85abfcce1df4cdb355705210005cce1689fba15feac713c87d2b2b0bf74012d700334f8b933eaf34dc86db5759d06a354d739f67b872ce1d551039f352d743adfc50bd5e1eef6a047db8304d6822597e09ef633e0d856c92f23c7ea6bfdb7cf14909ec06411920bbaeaa16c1b4fe01fb90533db998be9f6d53e2b57031b667486cd9ce719c99a7dc5a77073d3f441a441d366c5617ac4a961ff87d8b5e0bcf625cffe3819f3574bbcfb09802c4fdf030c9a142661e3d0baa44d3eef5d3823aa3f775022655b0116bc91a99e3640119bc131e600d2a8646483da9f20c103791ba01d9789e67ba002e645bef750e8228910b33253992e23a24e25b35fd1b285e16d4f78866b0697da10ddb186d48a764261a05a5d3b6a913362dfa864e53e3eb2fcb447c2ac7a408dedccdcb79459a1d60ff1ffbf4b74767ea8bd8aa812e92f2994b90312b55a8cfa80d7ee278e63fdb84e451dc99a10bbd570dbbea52fb219a558256eae76fa2df733b6b91f4085479371763b29a0b164adcd3094491efcad6e463b3f14d5b7db7a8d1f9a050dd060f3de592ece65e2a189b9f857c4709576b129e9c32719d880f3047a1a5e429288bad23c51f26ed32e023cefc587deb286dae7e31b2f239655d67d2e95b4bd673a9f27e5d634e81456acc1cc8960893c93e794b8b4f77fc7200d7cc6e1c1e6ac8599b596af1f6f0a91e271eec90b2cc4d66dabfd1e091d105c5987b015c46550b8d8033829656c3911770bc216d12097a2f77a9bd38f557c3ef15f5388bec2fb63385fe61db27e11a69d42f6b33f0e77e5038464c645bf81e3b2735b79f8e90456c4eda77010a52f301daaafda44f7a992cc41fcec6e0e85a90a79d4580d6bb856e888cb05777205f36ead94e0ef7c75bcf59fc8c3dcaee8c3a89c451ee964bbfec6ea19c25cfa4409b4a9bdfa5bd619b8410ea8a61a46cbfe222603f6a984df70187966a02646e1070992788d5b46f66130f50ed0cdfb1358bbe9b7ac37b1f2933adbff345c960a595b49051ff7789f58f0fe3df82e50d1685fa5a7af76234a721d973bac3840ad6fc9f5f68a54eb4c7b7bda57df647938099dde938de78569ef2192984d4f522e1632f4add687ccac31ffe9b155e48cc138455cefdf744dc5640e22c5a6f56d66a5aae419dc43f04c666ede629dd7a75dc62bce9b08fc470a4449bb12b7f4a5993e98070888617fd30cd763bd10684d83ce15b0981f70e38da2a626291b9555aff2d37d0c510f27a66e06958a9bc2d70d3fcb08fef019cbc1115e94d62161243a8a1f7617051f5a6ab2839b96953ab53187c9a96b1e4b76c962c4aa97415b717971e6c41c998b0dcd2612d52b1c04f96acea069a71ac0c93c31dac5ba5465d8cce4c9b0aa8e4119c47d228610ece36147a9af577fd4cb85f0339325b878a9d8045b94d79dec5f765973fdb0fc1411c38ef97549bf7b6f422722e596f0de04b7a31290970356fb57e56fa62cff7eebd6903929962fc96416537b3f50edc49235769fcaafbf66a1abe984f6832584975f23449735c10afaec31f1baca4c03c82fcb1d07200b954357c0c5a667095db298c34408cb4ec1dfbf146f86b56e2711157793d8276b7ae97515e3cee0158bf55c98072cdee0ca0f0955fe2d4e50bb0ea5cae7142b82f0d0571a060e3415d326aa05d84c20d164de15ac94ee82333190b031dcc56a2fd359272915fa03f44139eb339ff9d39ab1934009bfdd30fd1b111c7c35e260c6f7ee5feeb14aef5b0442b01ae9dc84e02d7684bcfda0a770df4d0a:
    print("Access Granted!")
    print(flag)
    exit(0)
else:
    print("Bad Key.")
    exit(0)
```

This is just math. We have to find

$$ a,b,c,x,y,z > 10^{3000}$$
$$a^3 + 12a^2c + 48ac^2 - b^3 - 18b^2c - 108bc^2 - 241c^3+\\
 3c^2x + 12c^2y - 48c^2z + 3cx^2 + 6cy^2 - 6cz^2 + x^3 + y^3=N$$

Wow. Insanely long equation. First step is to make some observations about the polynomial.

Firstly, there are 4 cubed terms, $a^3$, $-b^3$, $x^3$, $y^3$, and if you look closely, every other term contains $c$. Additionally, seeing $a^3 + 12a^2c + 48ac^2$ at the start of the equation makes me suspect I have to extract $(a+4c)^3$ which would produce those three coefficients. So, similarly, we can try to extract such cubed expressions for $b$, $x$ and $y$.

$$(a+4c)^3 - (b+6c)^3 + (x+c)^3 + (y+2c)^3 - 98 c^3 - 48 c^2 z - 6 cz^2 = N$$

Unfortunately, the $z$ cannot be factorized further, but we will make do. Transferring it to the other side,

$$(a+4c)^3 - (b+6c)^3 + (x+c)^3 + (y+2c)^3  = N+ 98 c^3 + 48 c^2 z + 6 cz^2$$

Seems like if we can figure out a general solution to expressing an integer as the sum of three of four cubes, we would solve this by just plugging in values of $c$ and $z$.

## Three cubes

So, first I check general solutions to sum of three cubes and get [this](https://en.wikipedia.org/wiki/Sums_of_three_cubes) which shows these two solutions:

$$(9b^4)^3 + (3b-9b^4)^3 + (1-9b^3)^3 = 1$$
$$(1+6c^3)^3 + (1-6c^3)^3 + (-6c^2)^3 = 2$$

which would imply a general solution for any $N = n^3$ or $N = 2n^3$.

So, we check the given $N$ for both forms (sage):
(I know that you can vary $c$ and $z$ to produce any number but I tunnel visioned really hard on this chall)
```py
floor(N^(1/3))^(3)==N, floor((N//2)^(1/3))^(3)==N//2
```

and the second expression yields True. So, N is double of a cube.

So, now we have to turn our expression of 3 positive cubes, 1 negative cube and a bunch of $c,z$ terms into 1 positive cube and 2 negative cubes.

So, arranging the terms as such:

$$ - (b+6c)^3 + (x+c)^3 + \bigg((y+2c)^3 +(a+4c)^3- 98 c^3 - 48 c^2 z - 6 cz^2\bigg) = N$$

We now need just need to convert 

$$(y+2c)^3 +(a+4c)^3- 98 c^3 - 48 c^2 z - 6 cz^2$$

into a negative cube. The way I decided to do this, was to set 

$$y, a, z = n_yc, n_ac, n_zc$$

Which would turn the whole expression into some $mc^3$. Then, I could just find such values of $n_y, n_a, n_z$ such that $m$ is a negative cube, and I would win.

Since we have literally 3 degrees of freedom, I assumed that 1 would be easily obtainable, and just went to sage to brute force a solution:

```py
for ny in range(1,50):
    for na in range(1,50):
        for nz in range(1,50):
            if (ny+2)^3 + (na+4)^3 - 98 - 48*nz - 6*nz^2==-1:
                print("ny = "+str(ny))
                print("na = "+str(na))
                print("nz = "+str(nz))
```

which quickly gave two solutions:

$$(n_y, n_a, n_z) = (8, 11, 23)$$
$$(n_y, n_a, n_z) = (13, 6, 23)$$

I just chose the second one. This now means that if we set

$$y, a, z = 13c, 6c, 23c$$

Then our total expression becomes 

$$ - (b+6c)^3 + (x+c)^3 - c^3 = N$$

And we can directly substitute our found solution.

Implementing this in python:

```py
import gmpy2
n=int(gmpy2.iroot(N//2,3)[0])
assert 2*n**3==N
A=(1+6*7**3)*n
B=(1-6*7**3)*n
C=(-6*7**2)*n
assert A**3 + B**3 + C**3 ==N
c=-C
b=-B-6*c
x=A-c
a=6*c
z=23*c
y=13*c


assert a**3 + 12*a**2*c + 48*a*c**2 - b**3 - 18*b**2*c - 108*b*c**2 - 241*c**3 + 3*c**2*x + 12*c**2*y - 48*c**2*z + 3*c*x**2 + 6*c*y**2 - 6*c*z**2 + x**3 + y**3 == N
assert a>10**3000 and b>10**3000 and c>10**3000 and x>10**3000 and y>10**3000 and z>10**3000
conn = remote("amt.rs", 31313)
conn.recv()
conn.send(str(a)+"\n")
conn.recv()
conn.send(str(b) +"\n")
conn.recv()
conn.send(str(c)+ "\n")
conn.recv()
conn.send(str(x)+ "\n")
conn.recv()
conn.send(str(y) +"\n")
conn.recv()
conn.send(str(z) +"\n")
conn.recv()
```

giving the flag `amateursCTF{h0w_d1d_y0u_stiLL_get_in_w1th0uT_MY_KEY?!?!?!??!?}`

## Four cubes

Wow, so simple right? Well, during the CTF I didn't realise that $N=2n^3$ for quite long, and for good reason. It is quite guessy to assume that, so now I will demonstrate how this is still solveable without that information, despite me failing to do so during the CTF.

Go back to this equation:

$$(a+4c)^3 - (b+6c)^3 + (x+c)^3 + (y+2c)^3  = N+ 98 c^3 + 48 c^2 z + 6 cz^2$$

We will now treat this as trying to find the sum of four cubes. Turns out, this is quite a well-researched problem. Doing some googling, these are the most helpful links:

[wikipedia](https://en.wikipedia.org/wiki/Sum_of_four_cubes_problem)

[alpertron](https://www.alpertron.com.ar/FCUBES.HTM)

Which provide case-by-case general solutions for sum of four cubes depending on $N$ being what mod what.

So we need a four cubes expression with exactly one negative cube, then we can possibly fit the value of $98 c^3 + 48 c^2 z + 6 cz^2$ to force $N$ into whatever.

Wikipedia has only one such solution:

$$(x+2)^3 + (6x-1)^3 + (8x-2)^3 + (-9x+2)^3 = 18x+7$$

But the issue is, $N+ 98 c^3 + 48 c^2 z + 6 cz^2$ has one common factor, and its 2. So we need a modulo expression that allows for even numbers. From the second link, the first such expression is:

$$(-9x+4)^3 + (x+4)^3 + (6x-2)^3 + (8x-4)^3 = 72x+56$$

Given that $N \equiv 34 \pmod{72}$, we just need to find $c,z$ such that

$$98 c^3 + 48 c^2 z + 6 cz^2 
\equiv 22 \pmod{72}$$

So, we go back to sage to bruteforce
```py
for c in range(72):
    for z in range(72):
        if (98*c^3 + 48*c^2*z + 6*c*z^2) % 72==22:
            print("c = "+str(c))
            print("z = "+str(z))
```

But somehow, this straight up has no solutions.

Scanning through the rest of the site, no other solution follows the form of having only one negative cube. Are we dead?

Not yet. Notice that if we take the negative of the entire equation and set $x$ to a negative value, we would still have one negative cube only, but instead of $56 \pmod{72}$, we would have the negative, which is $16 \pmod{72}$

$$(-9x-4)^3 + (x-4)^3 + (6x+2)^3 + (8x+4)^3 = 72x-56$$

Now, we need $c,z$ such that 

$$98 c^3 + 48 c^2 z + 6 cz^2 
\equiv 54 \pmod{72}$$

So, rerunning the bruteforce but for $54 \pmod{72}$ instead, the first solution we get is

$$c,z = 3,0 \pmod{72}$$

Then, we can just substitute sufficiently large values of such $c, z$ back in and use the general solution to solve for 4 cubes.

The code is as follows:

```py
c = 10**3000*72+3
z = 10**3000*72
X = ((N + 98*c**3 + 48*c**2*z + 6*c*z**2) + 56)//72

A = X-4
B = -9*X-4
C = 6*X+2
D = 8*X+4

a = A-4*c
b = -B - 6*c
x = C - c
y = D - 2*c

assert a**3 + 12*a**2*c + 48*a*c**2 - b**3 - 18*b**2*c - 108*b*c**2 - 241*c**3 + 3*c**2*x + 12*c**2*y - 48*c**2*z + 3*c*x**2 + 6*c*y**2 - 6*c*z**2 + x**3 + y**3 == N
assert a>10**3000 and b>10**3000 and c>10**3000 and x>10**3000 and y>10**3000 and z>10**3000
conn = remote("amt.rs", 31313)
conn.recv()
conn.send(str(a)+"\n")
conn.recv()
conn.send(str(b) +"\n")
conn.recv()
conn.send(str(c)+ "\n")
conn.recv()
conn.send(str(x)+ "\n")
conn.recv()
conn.send(str(y) +"\n")
conn.recv()
conn.send(str(z) +"\n")
conn.recv()
```

which also returns the flag.