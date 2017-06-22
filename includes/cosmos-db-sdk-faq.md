**1. In che modo i clienti verranno avvisati circa l'SDK in fase di ritiro?**

Microsoft invierà una notifica anticipata 12 mesi prima fine del supporto dell'SDK in fase di ritiro per agevolare la transizione a un SDK supportato. I clienti verranno anche informati tramite vari canali di comunicazione, ad esempio il portale di gestione di Azure, il centro sviluppatori e comunicazioni dirette indirizzate agli amministratori dei servizi indicati.

**2. I clienti potranno creare applicazioni usando la versione di DocumentDB SDK "in fase di ritiro" durante il periodo di 12 mesi?** 

Sì, durante il periodo di tolleranza di 12 mesi ai clienti sarà garantito l'accesso completo per creare, distribuire e modificare applicazioni tramite la versione di DocumentDB SDK "in fase di ritiro". Durante il periodo di tolleranza di 12 mesi, i clienti sono invitati a eseguire la migrazione a una versione supportata più recente di DocumentDB SDK.

**3. I clienti potranno creare e modificare applicazioni usando una versione di DocumentDB SDK ritirata dopo il periodo di notifica di 12 mesi?**

Dopo il periodo di notifica di 12 mesi, l'SDK verrà ritirato. La piattaforma DocumentDB non consentirà alcun tentativo di accesso a DocumentDB da applicazioni che usano una versione di SDK ritirata. Inoltre, Microsoft non fornirà più il supporto tecnico per la versione di SDK ritirata.

**4. Cosa accade alle applicazioni in esecuzione del cliente che usano una versione non supportata di DocumentDB SDK?**

Qualsiasi tentativo di connessione al servizio DocumentDB con una versione di SDK ritirata verrà rifiutato. 

**5. Le nuove funzioni e funzionalità verranno applicate anche a tutti gli SDK non ritirati?**

Le nuove funzioni e funzionalità verranno aggiunte solo alle nuove versioni. Se si usa una versione di SDK precedente non ritirata, le richieste inviate a DocumentDB continueranno a funzionare come in precedenza ma non sarà possibile accedere alle nuove funzionalità.  

**6. Come si deve procedere se non è possibile aggiornare l'applicazione prima della data del ritiro?**

Si consiglia di effettuare l'aggiornamento alla versione di SDK più recente quanto prima. Una volta che un SDK è stato contrassegnato per il ritiro, l'utente ha a disposizione 12 mesi per aggiornare l'applicazione. Se, per qualunque motivo, non è possibile completare l'aggiornamento dell'applicazione entro questo periodo di tempo, contattare il [team di Cosmos DB](mailto:askcosmosdb@microsoft.com) e richiedere assistenza prima della data prevista per il ritiro.

