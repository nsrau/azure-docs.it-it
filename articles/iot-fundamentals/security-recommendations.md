---
title: Raccomandazioni sulla sicurezza per Azure Internet | Microsoft Docs
description: Questo articolo riepiloga i passaggi aggiuntivi per garantire la sicurezza nella soluzione hub Azure.
author: dsk-2015
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dkshir
ms.custom: security-recommendations
ms.openlocfilehash: 55ca189d5f4622a395ffe603d7f0d6764db82f3d
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877214"
---
# <a name="security-recommendations-for-azure-internet-of-things-iot-deployment"></a>Raccomandazioni sulla sicurezza per la distribuzione di Azure Internet delle cose

Questo articolo contiene raccomandazioni sulla sicurezza per i servizi di Azure. L'implementazione di queste raccomandazioni ti aiuterà a soddisfare i tuoi obblighi di sicurezza come cliente di Azure e a migliorare la sicurezza complessiva per le tue soluzioni Internet delle cose. Per altre informazioni sulle funzionalità di sicurezza intrinseche offerte da Azure, vedere [la pagina relativa alla sicurezza degli oggetti da zero](iot-security-ground-up.md).

## <a name="general"></a>Generale

| Recommendation | Commenti |
|-|-|
| Rimanere sempre aggiornati | Usare le versioni più recenti delle piattaforme supportate, i linguaggi di programmazione, i protocolli e i Framework. |
| Mantieni chiavi di autenticazione sicure | Lasciare gli ID dispositivo e le relative chiavi di autenticazione fisicamente sicure dopo la distribuzione. In questo modo si eviterà una maschera di dispositivo dannoso come dispositivo registrato. |
| Usare gli SDK per dispositivi quando possibile | Gli SDK per dispositivi implementano un'ampia gamma di funzionalità di sicurezza, ad esempio la crittografia, l'autenticazione e così via, per facilitare lo sviluppo di un'applicazione per dispositivi affidabile e sicura. Per altre informazioni, vedere [comprendere e usare gli SDK dell'hub Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) . |


## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Recommendation | Commenti |
|-|-|
| Definire il controllo di accesso per l'hub | [Comprendere e definire il tipo di accesso](iot-security-deployment.md#securing-the-cloud) che ogni componente avrà nella soluzione hub Internet, in base alle funzionalità. Le autorizzazioni consentite sono il *Registro di sistema read*, *RegistryReadWrite*, *ServiceConnect*e *DeviceConnect*. I [criteri di accesso condiviso predefiniti nell'hub Internet](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#access-control-and-permissions) possono anche definire le autorizzazioni per ogni componente in base al ruolo. |
| Definire il controllo di accesso per i servizi back-end | I dati inseriti dalla soluzione hub Internet possono essere usati da altri servizi di Azure, ad [esempio Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/), [analisi di flusso](https://docs.microsoft.com/azure/stream-analytics/), [servizio app](https://docs.microsoft.com/azure/app-service/), app per la [logica](https://docs.microsoft.com/azure/logic-apps/)e [archiviazione BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction). Assicurarsi di comprendere e consentire le autorizzazioni di accesso appropriate, come documentato per questi servizi. |


## <a name="data-protection"></a>Protezione dati

| Recommendation | Commenti |
|-|-|
| Autenticazione protetta del dispositivo | Assicurarsi che la comunicazione tra i dispositivi e l'hub Internet sia sicura, usando [una chiave di identità univoca o un token di sicurezza](iot-security-deployment.md#iot-hub-security-tokens)oppure [un certificato X. 509](iot-security-deployment.md#x509-certificate-based-device-authentication) per ogni dispositivo. Utilizzare il metodo appropriato per [utilizzare i token di sicurezza basati sul protocollo scelto (MQTT, AMQP o HTTPS)](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security). |
| Comunicazione protetta del dispositivo | L'hub tutto protegge la connessione ai dispositivi usando Transport Layer Security (TLS) standard, supportando le versioni 1,2 e 1,0. Usare [TLS 1,2](https://tools.ietf.org/html/rfc5246) per garantire la massima sicurezza. |
| Proteggere le comunicazioni dei servizi | Hub di gestione delle cose fornisce gli endpoint per connettersi ai servizi back-end, ad esempio [archiviazione di Azure](/azure/storage/) o [Hub eventi](/azure/event-hubs) , usando solo il protocollo TLS e nessun endpoint viene esposto su un canale non crittografato. Una volta che questi dati raggiungono questi servizi back-end per l'archiviazione o l'analisi, assicurarsi di usare i metodi di sicurezza e crittografia appropriati per tale servizio e proteggere le informazioni riservate nel back-end. |


## <a name="networking"></a>Rete

| Recommendation | Commenti |
|-|-|
| Proteggere l'accesso ai dispositivi | Per evitare l'accesso indesiderato, è necessario che le porte hardware nei dispositivi siano minime. Inoltre, i meccanismi di compilazione per prevenire o rilevare manomissioni fisiche del dispositivo. Per informazioni dettagliate, leggere le procedure consigliate per la [sicurezza](iot-security-best-practices.md) . |
| Crea hardware sicuro | Incorporare le funzionalità di sicurezza, ad esempio l'archiviazione crittografata o la Trusted Platform Module (TPM), per rendere più sicuri i dispositivi e l'infrastruttura. Proteggere il sistema operativo e i driver del dispositivo aggiornati alle versioni più recenti e, se lo spazio è consentito, installare le funzionalità antivirus e antimalware. Leggere l' [architettura di sicurezza](iot-security-architecture.md) di Internet per capire come può contribuire a mitigare diverse minacce per la sicurezza. |


## <a name="monitoring"></a>Monitoraggio

| Recommendation | Commenti |
|-|-|
| Monitorare l'accesso non autorizzato ai dispositivi |  Usare la funzionalità di registrazione del sistema operativo del dispositivo per monitorare eventuali violazioni della sicurezza o manomissioni fisiche del dispositivo o delle relative porte. |
| Monitora la tua soluzione Internet delle cose dal cloud | Monitorare l'integrità complessiva della soluzione dell'hub Internet delle cose usando le [metriche in monitoraggio di Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics). |
| Configurare la diagnostica | Osservare attentamente le operazioni registrando gli eventi nella soluzione e quindi inviando i log di diagnostica a monitoraggio di Azure per ottenere la visibilità delle prestazioni. Per altre informazioni, vedere [monitorare e diagnosticare i problemi nell'hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitor-resource-health) Internet. |

## <a name="next-steps"></a>Passaggi successivi

Per gli scenari avanzati che coinvolgono Azure, potrebbe essere necessario prendere in considerazione requisiti di sicurezza aggiuntivi. Per altre informazioni, vedere [architettura di sicurezza](iot-security-architecture.md) di Internet.

