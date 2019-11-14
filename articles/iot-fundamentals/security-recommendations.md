---
title: Raccomandazioni sulla sicurezza per Azure Internet | Microsoft Docs
description: Questo articolo riepiloga i passaggi aggiuntivi per garantire la sicurezza nella soluzione hub Azure.
author: dsk-2015
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: article
ms.date: 11/13/2019
ms.author: dkshir
ms.custom: security-recommendations
ms.openlocfilehash: 0ada9a520a5be56444a1c3e746a68dbcf9275686
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048466"
---
# <a name="security-recommendations-for-azure-internet-of-things-iot-deployment"></a>Raccomandazioni sulla sicurezza per la distribuzione di Azure Internet delle cose

Questo articolo contiene raccomandazioni per la sicurezza. L'implementazione di questi consigli consentirà di soddisfare gli obblighi di sicurezza come descritto nel modello di responsabilità condivisa. Per ulteriori informazioni sulle attività svolte da Microsoft per soddisfare le responsabilità del provider di servizi, vedere [responsabilità condivise per cloud computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91).

Alcuni consigli inclusi in questo articolo possono essere monitorati automaticamente dal centro sicurezza di Azure. Il Centro sicurezza di Azure è la prima linea di difesa per la protezione delle risorse in Azure. Analizza periodicamente lo stato di sicurezza delle risorse di Azure per identificare le potenziali vulnerabilità di sicurezza. Fornisce quindi suggerimenti su come risolverli.

- Per altre informazioni sulle raccomandazioni del Centro sicurezza di Azure, vedere [raccomandazioni per la sicurezza nel centro sicurezza di Azure](../security-center/security-center-recommendations.md).
- Per informazioni sul centro sicurezza di Azure, vedere [che cos'è il Centro sicurezza di Azure?](../security-center/security-center-intro.md)

## <a name="general"></a>General

| Raccomandazione | Commenti | Supportato da ASC |
|-|----|--|
| Rimanere sempre aggiornati | Usare le versioni più recenti delle piattaforme supportate, i linguaggi di programmazione, i protocolli e i Framework. | - |
| Mantieni chiavi di autenticazione sicure | Lasciare gli ID dispositivo e le relative chiavi di autenticazione fisicamente sicure dopo la distribuzione. In questo modo si eviterà una maschera di dispositivo dannoso come dispositivo registrato. | - |
| Usare gli SDK per dispositivi quando possibile | Gli SDK per dispositivi implementano un'ampia gamma di funzionalità di sicurezza, ad esempio la crittografia, l'autenticazione e così via, per facilitare lo sviluppo di un'applicazione per dispositivi affidabile e sicura. Per altre informazioni, vedere [comprendere e usare gli SDK dell'hub Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) . | - |

## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso 

| Raccomandazione | Commenti | Supportato da ASC |
|-|----|--|
| Definire il controllo di accesso per l'hub | [Comprendere e definire il tipo di accesso](iot-security-deployment.md#securing-the-cloud) che ogni componente avrà nella soluzione hub Internet, in base alle funzionalità. Le autorizzazioni consentite sono il *Registro di sistema read*, *RegistryReadWrite*, *ServiceConnect*e *DeviceConnect*. I [criteri di accesso condiviso predefiniti nell'hub Internet](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#access-control-and-permissions) possono anche definire le autorizzazioni per ogni componente in base al ruolo. | - |
| Definire il controllo di accesso per i servizi back-end | I dati inseriti dalla soluzione hub Internet possono essere usati da altri servizi di Azure, ad [esempio Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/), [analisi di flusso](https://docs.microsoft.com/azure/stream-analytics/), [servizio app](https://docs.microsoft.com/azure/app-service/), app per la [logica](https://docs.microsoft.com/azure/logic-apps/)e [archiviazione BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction). Assicurarsi di comprendere e consentire le autorizzazioni di accesso appropriate, come documentato per questi servizi. | - |

## <a name="data-protection"></a>Protezione dati

| Raccomandazione | Commenti | Supportato da ASC |
|-|----|--|
| Autenticazione protetta del dispositivo | Assicurarsi che la comunicazione tra i dispositivi e l'hub Internet sia sicura, usando [una chiave di identità univoca o un token di sicurezza](iot-security-deployment.md#iot-hub-security-tokens)oppure [un certificato X. 509](iot-security-deployment.md#x509-certificate-based-device-authentication) per ogni dispositivo. Utilizzare il metodo appropriato per [utilizzare i token di sicurezza basati sul protocollo scelto (MQTT, AMQP o HTTPS)](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security). | - |
| Comunicazione protetta del dispositivo | L'hub tutto protegge la connessione ai dispositivi usando Transport Layer Security (TLS) standard, supportando le versioni 1,2 e 1,0. Usare [TLS 1,2](https://tools.ietf.org/html/rfc5246) per garantire la massima sicurezza. | - |
| Proteggere le comunicazioni dei servizi | Hub di gestione delle cose fornisce gli endpoint per connettersi ai servizi back-end, ad esempio [archiviazione di Azure](/azure/storage/) o [Hub eventi](/azure/event-hubs) , usando solo il protocollo TLS e nessun endpoint viene esposto su un canale non crittografato. Una volta che questi dati raggiungono questi servizi back-end per l'archiviazione o l'analisi, assicurarsi di usare i metodi di sicurezza e crittografia appropriati per tale servizio e proteggere le informazioni riservate nel back-end. | - |

## <a name="networking"></a>Rete

| Raccomandazione | Commenti | Supportato da ASC |
|-|----|--|
| Proteggere l'accesso ai dispositivi | Per evitare l'accesso indesiderato, è necessario che le porte hardware nei dispositivi siano minime. Inoltre, i meccanismi di compilazione per prevenire o rilevare manomissioni fisiche del dispositivo. Per informazioni dettagliate, leggere le procedure consigliate per la [sicurezza](iot-security-best-practices.md) . | - |
| Crea hardware sicuro | Incorporare le funzionalità di sicurezza, ad esempio l'archiviazione crittografata o la Trusted Platform Module (TPM), per rendere più sicuri i dispositivi e l'infrastruttura. Proteggere il sistema operativo e i driver del dispositivo aggiornati alle versioni più recenti e, se lo spazio è consentito, installare le funzionalità antivirus e antimalware. Leggere l' [architettura di sicurezza](iot-security-architecture.md) di Internet per capire come può contribuire a mitigare diverse minacce per la sicurezza. | - |

## <a name="monitoring"></a>Monitoraggio

| Raccomandazione | Commenti | Supportato da ASC |
|-|----|--|
| Monitorare l'accesso non autorizzato ai dispositivi |  Usare la funzionalità di registrazione del sistema operativo del dispositivo per monitorare eventuali violazioni della sicurezza o manomissioni fisiche del dispositivo o delle relative porte. | - |
| Monitora la tua soluzione Internet delle cose dal cloud | Monitorare l'integrità complessiva della soluzione dell'hub Internet delle cose usando le [metriche in monitoraggio di Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics). | - |
| Configurare la diagnostica | Osservare attentamente le operazioni registrando gli eventi nella soluzione e quindi inviando i log di diagnostica a monitoraggio di Azure per ottenere la visibilità delle prestazioni. Per altre informazioni, vedere [monitorare e diagnosticare i problemi nell'hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitor-resource-health) Internet. | - |

## <a name="next-steps"></a>Passaggi successivi

Per gli scenari avanzati che coinvolgono Azure, potrebbe essere necessario prendere in considerazione requisiti di sicurezza aggiuntivi. Per altre informazioni, vedere [architettura di sicurezza](iot-security-architecture.md) di Internet.

