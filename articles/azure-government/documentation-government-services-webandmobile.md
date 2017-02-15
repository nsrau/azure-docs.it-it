---
title: API Web e per dispositivi mobili per Azure per enti pubblici | Documentazione Microsoft
description: "Fornisce un confronto delle funzionalità e informazioni aggiuntive sullo sviluppo di applicazioni per Azure Government"
services: azure-government
cloud: gov
documentationcenter: 
author: brendalee
manager: zakramer
ms.assetid: a1e173a9-996a-4091-a2e3-6b1e36da9ae1
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 12/5/2016
ms.author: brendal
translationtype: Human Translation
ms.sourcegitcommit: 6b77e338e1c7f0f79ea3c25b0b073296f7de0dcf
ms.openlocfilehash: f0ba3e50c4281a0e8c365be075ae45ab72f6e82d

---
# <a name="azure-government-web--mobile"></a>App Web e per dispositivi mobili per Azure per enti pubblici
## <a name="app-services"></a>Servizi app
### <a name="variations"></a>Varianti
Servizio app di Azure è in genere disponibile in Azure per enti pubblici.

L'indirizzo per le app di Servizio app di Azure create in Azure per enti pubblici è diverso da quello per le app create nel cloud pubblico:

| Tipo di servizio | Azure Public | Azure Government |
| --- | --- | --- |
| Servizio app |*.azurewebsites.net |*.azurewebsites.us|

Alcune funzionalità di Servizio app disponibili nel cloud pubblico non sono ancora disponibili in Azure per enti pubblici:

- Ambienti del servizio app
- Distribuzione di app
    - Distribuzione continua (anteprima)
- Impostazioni
    - Integrazione rete virtuale (vNet) e connessioni ibride
    - Analisi della sicurezza
- Strumenti di sviluppo
    - Test delle prestazioni
    - Esplora risorse
    - Debug PHP
- Monitoraggio
    - Application Insights
    - Metriche per istanza
    - Traffico HTTP in tempo reale
    - Eventi dell'applicazione
    - Log FREB
- Supporto e risoluzione dei problemi
    - Advisor del Servizio app
    - Cronologia degli errori
    - Diagnostica distribuita come servizio
    - Attenuare il problema


### <a name="considerations"></a>Considerazioni
Le informazioni seguenti identificano il limite di Azure per enti pubblici per il Servizio app:

| Dati regolamentati o controllati consentiti | Dati regolamentati o controllati non consentiti |
| --- | --- |
| Dati immessi, archiviati ed elaborati in Servizio app di Azure che possono contenere dati di esportazione controllati. File binari in esecuzione in Servizio app di Azure. Autenticatori statici, ad esempio le password e i PIN delle smart card per l'accesso ai componenti della piattaforma Azure. Chiavi private o i certificati usati per gestire i componenti della piattaforma Azure. Stringhe di connessione SQL. Altri segreti di sicurezza o informazioni, ad esempio chiavi di crittografia, chiavi master, chiavi di archiviazione e certificati archiviati nei servizi di Azure. |I metadati non possono contenere dati soggetti al controllo all'esportazione. Questi metadati includono tutti i dati di configurazione immessi durante la creazione e la gestione del Servizio app di Azure. Non immettere dati regolamentati o controllati nei campi relativi a gruppi di risorse, nomi di risorse e tag delle risorse|

## <a name="next-steps"></a>Passaggi successivi
Per informazioni aggiuntive e aggiornamenti, sottoscrivere il [blog di Microsoft Azure per enti pubblici. ](https://blogs.msdn.microsoft.com/azuregov/)




<!--HONumber=Dec16_HO2-->


