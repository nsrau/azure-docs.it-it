---
title: Consigli per la sicurezza per l'IoT di Azure Documenti Microsoft
description: Questo articolo riepiloga passaggi aggiuntivi per garantire la sicurezza nella soluzione Hub IoT di Azure.This article summarizes additional steps to ensure security in your Azure IoT Hub solution.
author: dsk-2015
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: article
ms.date: 11/13/2019
ms.author: dkshir
ms.custom:
- security-recommendations
- amqp
- mqtt
ms.openlocfilehash: 5dd09988d37982c41b761688492bd2dc3642b2db
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81728989"
---
# <a name="security-recommendations-for-azure-internet-of-things-iot-deployment"></a>Consigli sulla sicurezza per la distribuzione di Azure Internet of Things (IoT)Security recommendations for Azure Internet of Things (IoT) deployment

Questo articolo contiene consigli sulla sicurezza per l'IoT. L'implementazione di queste raccomandazioni ti aiuterà a rispettare i tuoi obblighi di sicurezza come descritto nel nostro modello di responsabilità condivisa. Per ulteriori informazioni sulle operazioni eseguite da Microsoft per adempiere alle responsabilità dei provider di servizi, leggere [Responsabilità condivise per](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)il cloud computing .

Alcuni dei consigli inclusi in questo articolo possono essere monitorati automaticamente dal Centro sicurezza di Azure.Some of the recommendations included in this article can be automatically monitored by Azure Security Center. Azure Security Center is the first line of defense in protecting your resources in Azure. Analizza periodicamente lo stato di sicurezza delle risorse di Azure per identificare potenziali vulnerabilità della sicurezza. Fornisce quindi consigli su come affrontarli.

- Per altre informazioni sui consigli del Centro sicurezza di Azure, vedere Consigli sulla sicurezza in Centro sicurezza di Azure.For more information on Azure Security Center [recommendations,](../security-center/security-center-recommendations.md)see Security recommendations in Azure Security Center .
- Per informazioni sul Centro sicurezza di Azure, vedere Il Centro sicurezza di [Azure?](../security-center/security-center-intro.md)

## <a name="general"></a>Generale

| Recommendation | Commenti | Supportato da ASC |
|-|----|--|
| Aggiornamenti | Utilizzare le versioni più recenti di piattaforme supportate, linguaggi di programmazione, protocolli e framework. | - |
| Mantenere al sicuro le chiavi di autenticazione | Mantenere fisicamente sicuri gli ID del dispositivo e le relative chiavi di autenticazione dopo la distribuzione. In questo modo si eviterà un dispositivo dannoso mascherato come un dispositivo registrato. | - |
| Utilizzare gli SDK del dispositivo quando possibile | Gli SDK dei dispositivi implementano un'ampia gamma di funzionalità di sicurezza, ad esempio crittografia, autenticazione e così via, per facilitare lo sviluppo di un'applicazione per dispositivi affidabile e sicura. Per altre informazioni, vedere [Comprendere e usare gli SDK dell'hub IoT di Azure.See Understand and use Azure IoT Hub SDKs](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) for more information. | - |

## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso 

| Recommendation | Commenti | Supportato da ASC |
|-|----|--|
| Definire il controllo di accesso per l'hubDefine access control for the hub | [Comprendere e definire il tipo di accesso di ogni](iot-security-deployment.md#securing-the-cloud) componente nella soluzione dell'hub IoT, in base alla funzionalità. Le autorizzazioni consentite sono *Registry Read*, *RegistryReadWrite*, *ServiceConnect*e *DeviceConnect*. I criteri di accesso condiviso predefiniti [nell'hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#access-control-and-permissions) consentono inoltre di definire le autorizzazioni per ogni componente in base al ruolo. | - |
| Definire il controllo di accesso per i servizi back-endDefine access control for back-end services | I dati ingeriti dalla soluzione Hub IoT possono essere utilizzati da altri servizi di Azure, ad esempio [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/), [Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/), [App Service](https://docs.microsoft.com/azure/app-service/), App [Apps](https://docs.microsoft.com/azure/logic-apps/)e [archiviazione BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction). Assicurarsi di comprendere e consentire le autorizzazioni di accesso appropriate come documentato per questi servizi. | - |

## <a name="data-protection"></a>Protezione dei dati

| Recommendation | Commenti | Supportato da ASC |
|-|----|--|
| Autenticazione sicura del dispositivo | Garantire comunicazioni sicure tra i dispositivi e l'hub IoT, usando una chiave di [identità univoca o](iot-security-deployment.md#iot-hub-security-tokens)un token di sicurezza o [un certificato X.509 sul dispositivo](iot-security-deployment.md#x509-certificate-based-device-authentication) per ogni dispositivo. Utilizzare il metodo appropriato per utilizzare i token di [sicurezza in base al protocollo scelto (MQTT, AMQP o HTTPS).](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security) | - |
| Comunicazione sicura del dispositivo | IoT Hub protegge la connessione ai dispositivi utilizzando lo standard TLS (Transport Layer Security), supportando le versioni 1.2 e 1.0. Utilizzare [TLS 1.2](https://tools.ietf.org/html/rfc5246) per garantire la massima sicurezza. | - |
| Proteggere le comunicazioni dei servizi | Hub IoT fornisce endpoint per la connessione ai servizi back-end come Archiviazione di [Azure](/azure/storage/) o [Hub eventi](/azure/event-hubs) usando solo il protocollo TLS e nessun endpoint è esposto su un canale non crittografato. Una volta che questi dati raggiungono questi servizi back-end per l'archiviazione o l'analisi, assicurarsi di utilizzare i metodi di sicurezza e crittografia appropriati per tale servizio e proteggere le informazioni riservate nel back-end. | - |

## <a name="networking"></a>Rete

| Recommendation | Commenti | Supportato da ASC |
|-|----|--|
| Proteggere l'accesso ai dispositivi | Mantenere le porte hardware nei dispositivi al minimo per evitare l'accesso indesiderato. Inoltre, meccanismi di compilazione per prevenire o rilevare la manomissione fisica del dispositivo. Leggi le best practice per la [sicurezza IoT](iot-security-best-practices.md) per i dettagli. | - |
| Creare hardware sicuro | Incorpora funzionalità di sicurezza come l'archiviazione crittografata o il TPM (Trusted Platform Module) per garantire la sicurezza dei dispositivi e dell'infrastruttura. Mantenere il sistema operativo del dispositivo e i driver aggiornati alle versioni più recenti e, se lo spazio lo consente, installare funzionalità antivirus e antimalware. Leggi [l'architettura di sicurezza IoT](iot-security-architecture.md) per capire come questo può aiutare a mitigare diverse minacce alla sicurezza. | - |

## <a name="monitoring"></a>Monitoraggio

| Recommendation | Commenti | Supportato da ASC |
|-|----|--|
| Monitorare l'accesso non autorizzato ai dispositivi |  Utilizzare la funzionalità di registrazione del sistema operativo del dispositivo per monitorare eventuali violazioni della sicurezza o manomissioni fisiche del dispositivo o delle relative porte. | - |
| Monitora la tua soluzione IoT dal cloud | Monitorare l'integrità complessiva della soluzione Hub IoT usando le [metriche in Monitoraggio di Azure.](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics) | - |
| Configurare la diagnostica | Osservare attentamente le operazioni registrando gli eventi nella soluzione e quindi inviando i log di diagnostica a Monitoraggio di Azure per ottenere visibilità sulle prestazioni. Per altre informazioni, leggere [Monitorare e diagnosticare i problemi nell'hub IoT.Read Monitor and diagnose problems in your IoT hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitor-resource-health) for more information. | - |

## <a name="next-steps"></a>Passaggi successivi

Per scenari avanzati che coinvolgono l'IoT di Azure, potrebbe essere necessario considerare requisiti di sicurezza aggiuntivi. Per altre indicazioni, vedere Architettura di [sicurezza IoT.See IoT security architecture](iot-security-architecture.md) for more guidance.

