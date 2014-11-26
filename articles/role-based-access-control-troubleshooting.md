<properties urlDisplayName="" pageTitle="Role based access control troubleshooting" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Role based access control troubleshooting" authors="Stephen Siciliano"  solutions="" writer="" manager="" editor=""  />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="Stephen="" Siciliano" />

# Risoluzione dei problemi del controllo degli accessi in base al ruolo

## Introduzione

[Controllo degli accessi in base al ruolo](http://azure.microsoft.com/it-it/documentation/articles/role-based-access-control-configure/) è uno strumento efficace che consente di delegare con estrema precisione l'accesso alle risorse in Azure. Ciò significa che si può accordare a una determinata persona l'accesso alle risorse specifiche di cui ha bisogno. Tuttavia, a volte il modello di risorse di Azure può essere complicato e può risultare difficile comprendere esattamente quali autorizzazioni si stanno concedendo.

Questo documento illustra gli effetti prodotti dall'utilizzo di alcuni dei nuovi ruoli del portale di anteprima. Esistono tre ruoli predefiniti inclusi in questa versione:

-   Proprietario
-   Contributore
-   Lettore

Proprietari e contributori hanno accesso completo all'esperienza di gestione, con la differenza che il contributore non può concedere l'accesso ad altri utenti o gruppi. Il ruolo di lettore è maggiormente articolato e verrà quindi esaminato in maniera più approfondita. [Vedere questo articolo](http://azure.microsoft.com/it-it/documentation/articles/role-based-access-control-configure/) per maggiori dettagli sulle modalità di concessione dell'accesso.

## Carichi di lavoro dei siti web

### Con accesso in sola lettura

Se si concede a un utente l'accesso in sola lettura a un sito Web, o si dispone personalmente di tale tipo di accesso, è possibile che alcune funzionalità impreviste siano disattivate. Per le seguenti funzionalità di gestione è necessario disporre dell'accesso **in scrittura** al sito Web, come Contributore o Proprietario: tali funzionalità non saranno disponibili in uno scenario di sola lettura.

1.  Comandi, ad esempio avvio, interruzione e così via.
2.  Modifica di impostazioni quali la configurazione generale, le impostazioni di scalabilità, di backup e di monitoraggio.
3.  Accesso a credenziali di pubblicazione e altri segreti, quali le impostazioni delle app e le stringhe di connessione.
4.  Streaming dei log
5.  Configurazione dei log di diagnostica
6.  Console dei siti Web (prompt dei comandi)
7.  Distribuzioni attive e recenti, per la distribuzione continua del Git locale
8.  Spesa prevista
9.  Test Web
10. Rete virtuale, visibile per un lettore solo se in precedenza era già stata configurata una rete virtuale da un utente con accesso in scrittura.

Se non è possibile accedere a nessuna di queste tre parti, è necessario disporre dell'accesso Contributore per il sito Web.

### Gestione delle risorse correlate

La complessità dei siti Web è accentuata dalle interazioni tra alcune risorse diverse. Di seguito è illustra un tipico gruppo di risorse con alcuni siti Web:

![Gruppo di risorse di siti Web](./media/role-based-access-control-troubleshooting/Website-resource-model.png)

Di conseguenza, se si concede l'accesso al solo sito Web, molte funzionalità del blade del sito Web saranno completamente disabilitate.

1.  I seguenti elementi richiedono l'accesso al **Piano di hosting Web** corrispondente al sito Web in questione:

    -   Visualizzazione del livello di prezzo del sito Web, ad esempio Gratuito, Condiviso, Di base o Standard.
    -   Configurazione di scala, ossia n. di istanze, dimensione VM, impostazioni di scalabilità automatica.
    -   Quote, ad esempio memoria, larghezza di banda, CPU.

2.  I seguenti elementi richiedono l'accesso all'intero **Gruppo di risorse** che contiene il sito Web:

    -   Certificati e associazioni SSL: i certificati SSL possono infatti essere condivisi tra siti appartenenti allo stesso gruppo di risorse e area geografica.
    -   Regole di avviso
    -   Impostazioni di scalabilità automatica
    -   Componenti di Application Insights
    -   Test Web

## Carichi di lavoro delle macchine virtuali

Analogamente a quanto accade con i siti Web, alcune funzionalità del blade Macchina virtuale richiedono l'accesso in scrittura alla Macchina virtuale o ad altre risorse del gruppo di risorse.

Le risorse correlate delle macchine virtuali sono:

-   Nomi di dominio
-   Reti virtuali
-   Account di archiviazione
-   Regole di avviso

1.  I seguenti elementi richiedono l'accesso **in scrittura** alla Macchina virtuale:

    -   Endpoint
    -   Indirizzi IP
    -   Dischi
    -   Estensioni

2.  I seguenti elementi richiedono l'accesso in scrittura sia alla Macchina virtuale che al **Gruppo di risorse**, così come al nome di dominio, a cui appartiene:

    -   Set di disponibilità
    -   Set con carico bilanciato
    -   Regole di avviso

Se non è possibile accedere a nessuna di queste parti, è necessario richiedere all'amministratore l'accesso Contributore per il sito Web.

