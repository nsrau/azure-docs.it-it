---
title: Risoluzione dei problemi del controllo degli accessi in base al ruolo | Microsoft Docs
description: Assistenza per problemi o domande sulle risorse del controllo degli accessi in base al ruolo.
services: azure-portal
documentationcenter: na
author: kgremban
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2016
ms.author: kgremban

---
# Risoluzione dei problemi del controllo degli accessi in base al ruolo
## Introduzione
Il [controllo degli accessi in base al ruolo](role-based-access-control-configure.md) è una funzionalità utile che consente di delegare con estrema precisione l'accesso a risorse di Azure. Ciò significa che si può concedere a una determinata persona l'accesso alle risorse specifiche di cui ha bisogno. Tuttavia, a volte il modello di risorse di Azure può essere complicato e può risultare difficile comprendere esattamente quali autorizzazioni si stanno concedendo.

Questo documento illustra gli effetti prodotti dall'uso di alcuni dei ruoli nel portale di Azure. Questi tre ruoli coprono tutti i tipi di risorsa:

* Proprietario
* Collaboratore
* Lettore

Proprietari e collaboratori hanno accesso completo all'esperienza di gestione, ma il collaboratore non può concedere l'accesso ad altri utenti o gruppi. Il ruolo di lettore è maggiormente articolato e verrà quindi esaminato in maniera più approfondita. Per informazioni dettagliate su come concedere l'accesso, vedere [Controllo degli accessi in base al ruolo di Azure](role-based-access-control-configure.md).

## Carichi di lavoro del servizio app
### Funzionalità di accesso in scrittura
Se si concede a un utente l'accesso in sola lettura a un'unica app Web, sono disabilitate alcune funzionalità non prevedibili. Per le funzionalità di gestione seguenti è necessario avere accesso **in scrittura** a un'app Web, come Collaboratore o Proprietario. Tali funzionalità non saranno disponibili in uno scenario di sola lettura.

* Comandi, ad esempio avvio, interruzione e così via.
* Modifica di impostazioni quali la configurazione generale, le impostazioni di scalabilità, di backup e di monitoraggio.
* Accesso a credenziali di pubblicazione e altri segreti, quali le impostazioni delle app e le stringhe di connessione.
* Streaming dei log
* Configurazione dei log di diagnostica
* Console (prompt dei comandi)
* Distribuzioni attive e recenti, per la distribuzione continua del Git locale
* Spesa prevista
* Test Web
* Rete virtuale, visibile per un lettore solo se in precedenza era già stata configurata una rete virtuale da un utente con accesso in scrittura.

Se non è possibile accedere a nessuno di questi titoli, è necessario richiedere all'amministratore l'accesso come Collaboratore per l'app Web.

### Gestione delle risorse correlate
La complessità delle app Web è accentuata dalle interazioni tra alcune risorse diverse. Di seguito è illustrato un tipico gruppo di risorse con alcuni siti Web:

![Gruppo di risorse per app Web](./media/role-based-access-control-troubleshooting/website-resource-model.png)

Quindi, se si concede l'accesso solo all'app Web, gran parte delle funzionalità del pannello del sito Web nel portale di Azure saranno disabilitate.

Questi elementi richiedono accesso **in scrittura** al **piano di servizio App** che corrisponde al sito Web:

* Visualizzazione del piano tariffario dell'app Web, che può essere Free o Standard
* Configurazione di scalabilità, ossia numero di istanze, dimensione della macchina virtuale, impostazioni di scalabilità automatica
* Quote, ad esempio archiviazione, larghezza di banda e CPU

Gli elementi seguenti richiedono accesso **in scrittura** all'intero **gruppo di risorse** che contiene il sito Web:

* Certificati e associazioni SSL. I certificati SSL possono infatti essere condivisi tra siti appartenenti allo stesso gruppo di risorse e area geografica
* Regole di avviso
* Impostazioni di scalabilità automatica
* Componenti di Application Insights
* Test Web

## Carichi di lavoro delle macchine virtuali
Analogamente a quanto accade con le app Web, alcune funzionalità del blade della macchina virtuale richiedono l'accesso in scrittura alla macchina virtuale o ad altre risorse del gruppo di risorse.

Le macchine virtuali sono correlate a nomi di dominio, reti virtuali, account di archiviazione e regole di avviso.

Gli elementi seguenti richiedono accesso **in scrittura** alla **macchina virtuale**:

* Endpoint
* Indirizzi IP
* Dischi
* Estensioni

Gli elementi seguenti richiedono accesso **in scrittura** sia alla **macchina virtuale** che al **gruppo di risorse**, così come al nome di dominio a cui appartiene:

* Set di disponibilità
* Set con carico bilanciato
* Regole di avviso

Se non è possibile accedere a nessuno di questi titoli, è necessario richiedere all'amministratore l'accesso come Collaboratore per il gruppo di risorse.

## Altro
* [Controllo degli accessi in base al ruolo](role-based-access-control-configure.md): introduzione al controllo degli accessi in base al ruolo nel portale di Azure.
* [Ruoli predefiniti](role-based-access-built-in-roles.md): informazioni dettagliate sui ruoli predefiniti del controllo degli accessi in base al ruolo.
* [Ruoli personalizzati nel controllo degli accessi in base al ruolo di Azure](role-based-access-control-custom-roles.md): informazioni su come creare ruoli personalizzati per esigenze di accesso specifiche.
* [Creare un report della cronologia delle modifiche relative all'accesso](role-based-access-control-access-change-history-report.md): monitoraggio delle modifiche nelle assegnazioni dei ruoli nel controllo degli accessi in base al ruolo.

<!---HONumber=AcomDC_0713_2016-->