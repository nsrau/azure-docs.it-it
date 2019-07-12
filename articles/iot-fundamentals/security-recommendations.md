---
title: Consigli sulla sicurezza per Azure IoT | Microsoft Docs
description: Questo articolo riepiloga i passaggi aggiuntivi per garantire la sicurezza nella soluzione IoT Hub di Azure.
author: dsk-2015
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dkshir
ms.custom: security-recommendations
ms.openlocfilehash: 4416f3149c33a0c9a437b2fbd6a48729a5a7f044
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722862"
---
# <a name="security-recommendations-for-azure-internet-of-things-iot-deployment"></a>Raccomandazioni sulla sicurezza per la distribuzione di Azure Internet delle cose (IoT)

Questo articolo contiene raccomandazioni sulla sicurezza per i servizi IoT di Azure. Implementazione di queste indicazioni della Guida in linea è soddisfare gli obblighi di sicurezza per i clienti di Azure IoT e verranno migliorerà la sicurezza complessiva per le soluzioni IoT. Per altre informazioni sulle funzionalità di protezione intrinseco fornita da Azure IoT, leggere [sicurezza IoT sin dall'inizio](iot-security-ground-up.md).

## <a name="general"></a>Generale

| Recommendation | Commenti |
|-|-|
| Rimanere aggiornati | Usare le versioni più recenti delle piattaforme supportate, linguaggi di programmazione, protocolli e Framework. |
| Proteggere le chiavi di autenticazione | Mantenere gli ID dispositivo e le relative chiavi di autenticazione fisicamente sicura dopo la distribuzione. Questo modo si evita un mascheramento dannoso dispositivo come un dispositivo registrato. |
| Usare gli SDK del dispositivo quando possibile | Gli SDK per dispositivi implementano un'ampia gamma di funzionalità di sicurezza, ad esempio, crittografia, autenticazione e così via, per facilitare lo sviluppo di un'applicazione del dispositivo affidabile e sicura. Visualizzare [comprendere e usare SDK dell'Hub IoT di Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) per altre informazioni. |


## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Recommendation | Commenti |
|-|-|
| Definire il controllo di accesso per l'hub | [Comprendere e definire il tipo di accesso](iot-security-deployment.md#securing-the-cloud) ogni componente sarà necessario nella soluzione IoT Hub, basati sulle funzionalità. Le autorizzazioni consentite sono *lettura del Registro di sistema*, *RegistryReadWrite*, *ServiceConnect*, e *DeviceConnect*. Default [criteri di accesso dell'hub IoT condiviso](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#access-control-and-permissions) consentono anche di definire le autorizzazioni per ogni componente in base al relativo ruolo. |
| Definire il controllo di accesso per i servizi back-end | I dati inseriti dalla soluzione IoT Hub possono essere utilizzati da altri servizi di Azure, ad esempio [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/), [Stream Analitica](https://docs.microsoft.com/azure/stream-analytics/), [servizio App](https://docs.microsoft.com/azure/app-service/), [App per la logica](https://docs.microsoft.com/azure/logic-apps/), e [archivio Blob](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction). Assicurarsi di comprendere e consentire le autorizzazioni di accesso appropriati, come documentato per questi servizi. |


## <a name="data-protection"></a>Protezione dati

| Recommendation | Commenti |
|-|-|
| Proteggere l'autenticazione del dispositivo | Stabilire comunicazioni protette tra i dispositivi e l'hub IoT, usando [una chiave di identità univoca oppure security token](iot-security-deployment.md#iot-hub-security-tokens), o [un certificato X.509 sul dispositivo](iot-security-deployment.md#x509-certificate-based-device-authentication) per ogni dispositivo. Usare il metodo appropriato per [usare i token di sicurezza basati sul protocollo scelto (MQTT, AMQP o HTTPS)](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security). |
| Comunicazione protetta dei dispositivi | L'IoT Hub consente di proteggere la connessione ai dispositivi usando Transport Layer Security (TLS) standard, che supporta le versioni 1.2, 1.2 e 1.0. Uso [TLS 1.2](https://tools.ietf.org/html/rfc5246) per garantire la massima sicurezza. |
| Proteggere le comunicazioni dei servizi | L'IoT Hub fornisce gli endpoint per connettersi ai servizi back-end, ad esempio [archiviazione di Azure](/azure/storage/) oppure [hub eventi](/azure/event-hubs) usando solo il protocollo TLS e nessun endpoint viene esposto in un canale non crittografato. Quando i dati raggiungono questi servizi back-end per l'archiviazione o analisi, assicurarsi di utilizzare metodi appropriati di sicurezza e la crittografia per il servizio e proteggere le informazioni riservate al back-end. |


## <a name="networking"></a>Rete

| Recommendation | Commenti |
|-|-|
| Proteggere l'accesso ai dispositivi | Mantenere hardware porte nei dispositivi per i requisiti minimi per evitare accessi indesiderati. Inoltre, creare meccanismi per evitare o rilevare manomissioni fisiche del dispositivo. Lettura [consigliate di sicurezza IoT](iot-security-best-practices.md) per informazioni dettagliate. |
| Creare un hardware sicuro | Incorporare funzionalità di sicurezza, ad esempio un archivio crittografato o modulo TPM (Trusted Platform), per rendere più sicuro i dispositivi e infrastruttura. Mantenere il sistema operativo del dispositivo e i driver aggiornati alle versioni più recenti e, se lo spazio è sufficiente, installare le funzionalità antivirus e antimalware. Lettura [architettura di sicurezza IoT](iot-security-architecture.md) per comprendere come ciò consenta di attenuare diversi rischi di protezione. |


## <a name="monitoring"></a>Monitoraggio

| Recommendation | Commenti |
|-|-|
| Monitorare l'accesso non autorizzato ai dispositivi |  Usare la funzionalità di registrazione del sistema operativo dispositivo per monitorare eventuali violazioni della sicurezza o manomissioni fisiche del dispositivo o le relative porte. |
| Monitorare la tua soluzione IoT dal cloud | Monitorare l'integrità complessiva della soluzione IoT Hub usando il [le metriche in Monitoraggio di Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics). |
| Configurare la diagnostica | Osservare da vicino le operazioni di registrazione degli eventi nella soluzione e quindi inviando i log di diagnostica a monitoraggio di Azure per ottenere visibilità sulle prestazioni. Lettura [monitorare e diagnosticare i problemi nell'hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitor-resource-health) per altre informazioni. |

## <a name="next-steps"></a>Passaggi successivi

Per scenari avanzati con Azure IoT, potrebbe essere necessario prendere in considerazione i requisiti di sicurezza aggiuntive. Visualizzare [architettura di sicurezza IoT](iot-security-architecture.md) per altro materiale sussidiario.

