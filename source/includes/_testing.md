# Sandkassi (prófunargögn)

```shell
curl https://api.testing.kass.is/v1/payments \
    -u kass_test_auth_token: \
    -d @payment_request.json
```

Hægt er að senda rukkanir á prófunarsvæði með því að nota aðgangskóðann `kass_test_auth_token`. Fyrir neðan má finna lista af símanúmerum sem senda má rukkun á og skýringu á viðbrögðum kerfisins.

### HTTP

`POST https://api.testing.kass.is/v1/payments`

Símanúmer | Skýring
----- | ------
100 1000 | Rukkun send á þetta númer rennur út eftir 90 sekúndur.
100 1001 | Rukkun send á þetta númer verður sjálfkrafa merkt **greidd**.
100 1002 | Rukkun send á þetta númer verður sjálfkrafa merkt **hafnað**.
