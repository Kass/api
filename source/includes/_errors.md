# Mögulegir villukóðar

<aside class="notice">
API skilar alltaf HTTP 200 OK. Þessir villukóðar eiga eingöngu við um villukóða í JSON svargögnum.
</aside>

Kass API styðst við eftirfarandi villukóða:

Villukóði | Lykill | Þýddur texti | Ástæða
--------- | ------ | ------------ | ------
100 | merchant_not_found | Söluaðili fannst ekki | Rangur aðgangskóði notaður í Basic Auth.
101 | merchant_account_locked | Söluaðili ekki virkur | Aðgangur söluaðila er lokaður.
102 | merchant_signature_incorrect | Undirritun ekki rétt | Undirritun söluaðila á innsendum gögnum ekki rétt.
200 | recipient_not_found | Viðtakandi fannst ekki | Enginn viðtakandi fannst með símanúmerið eða notandanafnið sem sent var inn.
201 | merchant_cannot_be_recipient | Söluaðili getur ekki verið viðtakandi | Ekki er hægt að senda rukkun á söluaðila.
300 | payment_not_found | Greiðsla fannst ekki | Greiðsla með uppgefnu ID fannst ekki.
400 | invalid_data | Innsend gögn eru ekki á réttu formi | Annað hvort vantar upplýsingar í innsend JSON gögn eða hluti þeirra er á röngu formi.
500 | system_error | Kerfisvilla - prófaðu aftur | Eitthvað leiðinlegt kom upp á í Kass kerfinu.
