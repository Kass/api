---
title: Kass API v1

language_tabs:
  - shell
  - csharp
  - go
  - php
  - ruby

toc_footers:
  - <a href='https://github.com/kass'>github.com/kass</a>
  - <a href='https://kass.is'>Kass - milli vina</a>

includes:
  - errors

search: true
---

# Inngangur

Velkomin í skjölun á Kass API v1.

# Auðkenning

Hver aðgerð í Kass API notar HTTP Basic Auth auðkenningu (sjá curl -u í dæmum). Aðgangskóði söluaðila er sendur inn sem notandanafn á meðan svæðið fyrir lykilorð er tómt.

`-u um2JjfnJbEUJnCpjKiV94jqp:`

<aside class="notice">
Mundu að skipta út <code>um2JjfnJbEUJnCpjKiV94jqp</code> fyrir þinn aðgangskóða.
</aside>

# Stofna rukkun

```shell
curl https://api.kass.is/v1/payments \
    -u um2JjfnJbEUJnCpjKiV94jqp: \
    -d @payment_request.json
```

> Dæmi um innsend JSON gögn (payment_request.json)

```json
{
    "amount": 2199,
    "description": "Kass bolur",
    "image_url": "https://photos.kassapi.is/kass/kass-bolur.jpg",
    "order": "ABC123",
    "recipient": "7728440",
    "notify_url": "https://example.com/callbacks/kass"
}
```

> Dæmi um JSON svargögn ef aðgerð tókst

```json
{
    "success": true,
    "id": "3e6975e8-77cb-48b7-7722-3dfe47677bbc",
    "created": 1458748385
}
```

> Dæmi um JSON svargögn ef upp kom villa

```json
{
    "success": false,
    "error": {
        "code": "200",
        "key": "recipient_not_found",
        "message": "Viðtakandi fannst ekki"
    }
}
```

Þessi aðgerð stofnar rukkun og sendir hana samstundis á viðtakanda. Viðtakandinn hefur þá 90 sekúndur til að bregðast við rukkuninni, hvort sem er að greiða eða hafna. Eftir þann tíma rennur rukkunin út og viðtakandi getur ekki lengur greitt.

### HTTP

`POST https://api.kass.is/v1/payments`

### Skýring á innsendum svæðum

Svæði | Tegund | Skýring
--------- | ------- | -----------
amount | Tala | Heildarupphæð greiðslu til rukkunar, skráð án aukastafa.
description | Strengur | Skilaboð til kaupanda. Valfrjálst svæði.
image_url | Strengur | Slóð að mynd sem geymd er á vef söluaðila. Kass appið sækir myndina út frá slóðinni þannig að kaupandinn sjái hana. Valfrjálst svæði.
order | Strengur | Pöntunarnúmer sem söluaðili getur sent með. Númerið er sent til baka til kerfis söluaðila eftir að kaupandi greiðir (notify_url). Kaupandi sér ekki númerið. Valfrjálst svæði.
recipient | Strengur | Notandanafn eða símanúmer viðtakanda. Styður aðeins einn viðtakanda í einu. Símanúmer má innihalda landkóða (+354) en þarf þess ekki.
notify_url | Strengur | Slóð hjá söluaðila sem Kass kerfið kallar í eftir að kaupandi hefur greitt. Sjá nánar kaflann Sjálfvirk tilkynning frá Kass.

### Skýring á svæðum í svargögnum

Svæði | Tegund | Skýring
----- | ------ | -------
success | Sanngildi | Skilar true ef öll gögn eru rétt og rukkun hefur verið send á viðtakandann.
id | Strengur	| Númer rukkunar sem er notað til að athuga stöðu.
created | Tala | Tímasetningin þegar rukkunin var stofnuð. Unix tímastimpill.
error | JSON | Upplýsingar um villu ef success gildið er false. Sjá nánar um villukóða í kaflanum Mögulegir villukóðar.

# Sækja upplýsingar um rukkun

```shell
curl https://api.kass.is/v1/payments/3e6975e8-77cb-48b7-7722-3dfe47677bbc \
    -u um2JjfnJbEUJnCpjKiV94jqp:
```

> Dæmi um JSON svargögn

```json
{
    "id": "3e6975e8-77cb-48b7-7722-3dfe47677bbc",
    "transaction_id": "a917be59-f35a-478f-a5d9-19bf467972ad",
    "amount": 2199,
    "description": "Kass bolur",
    "image_url": "https://photos.kassapi.is/kass/kass-bolur.jpg",
    "status": "paid",
    "order": "abc123",
    "created": 1458748385,
    "updated": 1458748422,
    "expires": 1458748475
}
```

Skilar upplýsingum um rukkun. Ef eingöngu er verið að athuga stöðu rukkunar, sjá kaflann Athuga stöðu á rukkun.

### HTTP

`GET https://api.kass.is/v1/payments/[id]`

### Skýring á svæðum

Svæði | Tegund | Skýring
----- | ------ | -------
id | Strengur| Númer rukkunar.
transaction_id | Strengur | Færslunúmer ef kaupandi hefur greitt. Ef rukkunin er ógreidd, útrunnin eða var hafnað er ekkert númer sent.
amount | Tala | Upphæð sem send var inn þegar rukkun var stofnuð.
description | Strengur | Skilaboð til kaupanda sem send voru inn þegar rukkun var stofnuð.
image_url | Strengur | Slóð að mynd sem send var inn þegar rukkun var stofnuð.
status | Strengur | Staðan á rukkuninni.<br>**pending** = Ógreidd.<br>**paid** = Kaupandi greiddi.<br>**rejected** = Kaupandi hafnaði eða rukkun rann út.
order | Strengur | Pöntunarnúmer sem söluaðili sendi inn þegar rukkun var stofnuð.
created | Tala | Tímasetningin þegar rukkunin var stofnuð. Unix tímastimpill.
updated | Tala | Tímasetningin þegar stöðu á rukkuninni var breytt. Ef staðan er óbreytt þá er þessi tími sami og created_at. Unix tímastimpill.
expires | Tala | Tímasetningin þegar rukkunin rennur út og viðtakandi getur ekki lengur greitt. Unix tímastimpill.

# Fella niður rukkun

```shell
curl -X DELETE https://api.kass.is/v1/payments/3e6975e8-77cb-48b7-7722-3dfe47677bbc \
    -u um2JjfnJbEUJnCpjKiV94jqp:
```

> Dæmi um JSON svargögn

```json
{
    "id": "3e6975e8-77cb-48b7-7722-3dfe47677bbc",
    "status": "rejected",
    "expires": 1458748475
}
```

Fellir niður rukkun samstundis þannig að notandi getur ekki lengur greitt.

### HTTP

`DELETE https://api.kass.is/v1/payments/[id]`

### Skýring á svæðum

Svæði | Tegund | Skýring
----- | ------ | -------
id | Strengur | Númer rukkunar.
status | Strengur | Staðan á rukkuninni.<br>**rejected** = Kaupandi hafnaði eða rukkun rann út.
expires | Tala | Tímasetningin þegar rukkunin var felld niður. Unix tímastimpill.

# Athuga stöðu á rukkun

```shell
curl https://api.kass.is/v1/payments/3e6975e8-77cb-48b7-7722-3dfe47677bbc/status \
    -u um2JjfnJbEUJnCpjKiV94jqp:
```

> Dæmi um JSON svargögn

```json
{
    "id": "3e6975e8-77cb-48b7-7722-3dfe47677bbc",
    "status": "pending",
    "expires": 1458748475
}
```

Skilar stöðunni á rukkun, þ.e.a.s. hvort hún sé ógreidd, hafi verið greidd, hafi verið hafnað eða sé útrunnin.

### HTTP

`GET https://api.kass.is/v1/payments/[id]/status`

### Skýring á svæðum

Svæði | Tegund | Skýring
----- | ------ | -------
id | Strengur | Númer rukkunar.
status | Strengur | Staðan á rukkuninni.<br>**pending** = Ógreidd.<br>**paid** = Kaupandi greiddi.<br>**rejected** = Kaupandi hafnaði eða rukkun rann út.
expires | Tala | Tímasetningin þegar rukkunin rennur út og viðtakandi getur ekki lengur greitt. Unix tímastimpill.

# Sjálfvirk tilkynning frá Kass (notification callback)

> Dæmi um JSON gögn

```json
{
    "payment_id": "3e6975e8-77cb-48b7-7722-3dfe47677bbc",
    "transaction_id": "a917be59-f35a-478f-a5d9-19bf467972ad",
    "amount": 2199,
    "status": "paid",
    "order": "abc123",
    "completed": 1458748422,
    "signature": "84f60f56af1bab4f4866e3a06ae28e37b71863809addabc7fc3d9b71ee9813ac"
}
```

Þegar staðan á rukkun breytist, hvort sem kaupandi greiðir, hafnar eða rukkun rennur út, er POST-að á notify_url slóðina, sem skilgreind var þegar rukkunin var stofnuð, með upplýsingum um greiðsluna. Svarið er undirritað.

### Skýring á svæðum

Svæði | Tegund | Skýring
----- | ------ | -------
payment_id | Strengur | Númer rukkunar.
transaction_id | Strengur | Færslunúmer ef kaupandi hefur greitt. Ef rukkunin er ógreidd, útrunnin eða var hafnað er ekkert númer sent.
amount | Tala | Upphæð sem send var inn þegar rukkun var stofnuð.
status | Strengur | Staðan á rukkuninni.<br>**paid** = Kaupandi greiddi.<br>**rejected** = Kaupandi hafnaði eða rukkun rann út.
order | Strengur | Pöntunarnúmer sem söluaðili sendi inn þegar rukkun var stofnuð.
completed | Tala | Tímasetningin þegar rukkun lokaðist og staðan breyttist úr pending yfir í **paid** eða **rejected**. Unix tímastimpill.
signature | Strengur | Undirritun sem söluaðili getur borið saman við til að staðfesta að svarið sé réttmætt.

## Undirritun

> Dæmi um samsettan streng

```
3e6975e8-77cb-48b7-7722-3dfe47677bbc&a917be59-f35a-478f-a5d9-19bf467972ad&abc123&2199&paid&1458748422
```

> Dæmi

```shell
** Veldu forritunarmál efst á síðunni **
```

```csharp
string key = "um2JjfnJbEUJnCpjKiV94jqp";
string body = "3e6975e8-77cb-48b7-7722-3dfe47677bbc&a917be59-f35a-478f-a5d9-19bf467972ad&abc123&2199&paid&1458748422";
byte[] keyBytes = Encoding.UTF8.GetBytes(key);
HMACSHA256 hasher = new HMACSHA256(keyBytes);
byte[] bodyBytes = hasher.ComputeHash(Encoding.UTF8.GetBytes(body));
string signature = BitConverter.ToString(bodyBytes).Replace("-", "");
```

```go
import (
	"crypto/hmac"
	"crypto/sha256"
	"encoding/hex"
)
var key = []byte("um2JjfnJbEUJnCpjKiV94jqp")
var body = "3e6975e8-77cb-48b7-7722-3dfe47677bbc&a917be59-f35a-478f-a5d9-19bf467972ad&abc123&2199&paid&1458748422"
var h = hmac.New(sha256.New, key)
h.Write([]byte(body))
var signature = hex.EncodeToString(h.Sum(nil))
```

```php
<?php
$key = 'um2JjfnJbEUJnCpjKiV94jqp';
$body = utf8_encode('3e6975e8-77cb-48b7-7722-3dfe47677bbc&a917be59-f35a-478f-a5d9-19bf467972ad&abc123&2199&paid&1458748422');
$signature = hash_hmac('sha256', $body, $key);
?>
```

```ruby
require 'openssl'

key = 'um2JjfnJbEUJnCpjKiV94jqp'
body = '3e6975e8-77cb-48b7-7722-3dfe47677bbc&a917be59-f35a-478f-a5d9-19bf467972ad&abc123&2199&paid&1458748422'
digest = OpenSSL::Digest.new('sha256')
signature = OpenSSL::HMAC.hexdigest(digest, key, body)
```

> Dæmi um fullbúna undirritun

```
84f60f56af1bab4f4866e3a06ae28e37b71863809addabc7fc3d9b71ee9813ac
```

Undirritunin er strengur sem er settur saman úr eftirfarandi einingum og síðan hashaður með HMAC SHA256 þar sem aðgangskóðinn er lykillinn.

`payment_id&transaction_id&order&amount&status&completed`
