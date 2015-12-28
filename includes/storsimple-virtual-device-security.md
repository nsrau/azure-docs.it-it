<!--v-sharos 10/13/2105 virtual device security-->

Quando si utilizza il dispositivo virtuale StorSimple, tenere presenti le seguenti considerazioni sulla sicurezza:

- La sottoscrizione a Microsoft Azure consente di proteggere il dispositivo virtuale. Ciò significa che se l'utente utilizza il dispositivo virtuale in presenza di una sottoscrizione ad Azure compromessa, anche i dati memorizzati nel dispositivo virtuale possono essere vulnerabili.

- La chiave pubblica del certificato utilizzato per crittografare i dati archiviati in Azure StorSimple viene resa disponibile per il portale Azure; la chiave privata viene conservata nel dispositivo StorSimple. Nel dispositivo virtuale StorSimple le chiavi pubbliche e private vengono archiviate entrambe in Azure.

- Il dispositivo virtuale è ospitato nel data center di Microsoft Azure.

<!---HONumber=AcomDC_1217_2015-->