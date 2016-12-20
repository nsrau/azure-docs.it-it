---
title: Guida alla risoluzione dei problemi di Azure Mobile Engagement - Servizio
description: Guide alla risoluzione dei problemi di Azure Mobile Engagement
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: dwrede
editor: 
ms.assetid: 8b4275da-c0b4-4690-824a-48e9d7a1fc6e
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 01862d158f4771989ae6eb3b20fe8e080123a231


---
# <a name="troubleshooting-guide-for-service-issues"></a>Guida alla risoluzione dei problemi relativi al servizio
Di seguito sono indicati possibili problemi relativi all'esecuzione di Azure Mobile Engagement.

## <a name="service-outages"></a>Interruzioni di servizio
### <a name="issue"></a>Problema
* Problemi che sembrano essere causati da interruzioni del servizio Azure Mobile Engagement.

### <a name="causes"></a>Cause
* I problemi che sembrano dovuti a interruzioni del servizio Azure Mobile Engagement possono avere diverse cause:
  * Problemi isolati visualizzati originariamente come sistemici in Azure Mobile Engagement.
  * Problemi noti causati da interruzioni del server (non sempre visualizzati nello stato del server).
  * Ritardi di pianificazione, errori di destinazione, problemi nell'aggiornamento del badge, interruzione della raccolta dei dati da parte dei sistemi statistici, interruzione del funzionamento delle notifiche push e delle API, impossibilità di creazione di nuovi utenti o app, errori DNS e di timeout nell'interfaccia utente, nell'API o nelle app del dispositivo.
  * Interruzioni di dipendenza cloud [Stato del servizio Azure](http://status.azure.com/)
  * Interruzione di dipendenza servizi di notifica push (PNS, Push Notification Services)
  * Interruzioni di App Store

1) Per verificare se il problema è sistemico, eseguire il test della stessa funzione da un elemento differente

* Applicazione integrata di Azure Mobile Engagement
* Dispositivo di test
* Versione del sistema operativo del dispositivo di test
* Campagna
* Account utente dell'amministratore
* Browser (Internet Explorer, Firefox, Chrome, ecc.)
* Computer

2) Per verificare se il problema riguarda soltanto l'interfaccia utente o l'API:

* Verificare la stessa funzione dall'interfaccia utente di Azure Mobile Engagement e dell'API di Azure Mobile Engagement.

3) Per verificare se il problema riguarda la rete dati:

* Eseguire il test mentre si è connessi a Internet tramite Wi-Fi e mentre si è connessi tramite la rete dati 3G.
* Verificare che il firewall non blocchi porte o indirizzi IP di Azure Mobile Engagement.

4) Per verificare se il problema riguarda il dispositivo:

* Verificare se il dispositivo è in grado di connettersi a Azure Mobile Engagement con un'altra app integrata di Azure Mobile Engagement.
* Verificare che sia possibile generare eventi, processi e arresti anomali del sistema dal telefono che può essere visualizzato nell'interfaccia utente di Azure Mobile Engagement. 
* Verificare se è possibile inviare notifiche push dall'interfaccia utente di Azure Mobile Engagement sul dispositivo in base al relativo ID dispositivo. 

5) Per verificare se il problema riguarda l'app:

* Installare e testare l'applicazione da un emulatore anziché da un dispositivo fisico:

6) Per verificare se il problema riguarda gli aggiornamenti relativi al sistema operativo del dispositivo dell'utente finale ed è necessario aggiornare l'SDK per risolvere:

* Verificare l'applicazione in dispositivi diversi con diverse versioni del sistema operativo.
* Confermare che si utilizza la versione più recente dell’SDK.

## <a name="connectivity-and-incorrect-information-issues"></a>Problemi di connettività e di informazioni non corrette
### <a name="issue"></a>Problema
* Problemi di accesso all'interfaccia utente di Azure Mobile Engagement.
* Errori di connessione relativi alle API di Azure Mobile Engagement.
* Problemi relativi al caricamento dei tag sulle informazioni dell'app usando l'API del dispositivo.
* Problemi relativi al download di registri o di dati esportati da Azure Mobile Engagement.
* Informazioni non corrette visualizzate nell'interfaccia utente di Azure Mobile Engagement.
* Informazioni non corrette visualizzate nei registri di Azure Mobile Engagement.

### <a name="causes"></a>Cause
* Confermare che l'account dispone delle autorizzazioni necessarie a eseguire l'attività.
* Confermare che il problema non è relativo a un solo computer o alla propria rete locale.
* Confermare che il servizio Azure Mobile Engagement non ha riportato interruzioni.
* Confermare che i file relativi al tag di informazioni sull'app seguono tutte le regole seguenti:
  * Usare solo il set di caratteri UTF8 (set di caratteri ANSI non supportato).
  * Usare la virgola "," come separatore. È possibile aprire una richiesta di assistenza per richiedere di non usare la virgola "," come separatore, ma un altro carattere, ad esempio il punto e virgola ";".
  * Usare tutte le lettere minuscole per i valori Boolean "true" e "false".
  * Usare un file di dimensioni inferiori a 35 MB (limite consentito).




<!--HONumber=Nov16_HO3-->


