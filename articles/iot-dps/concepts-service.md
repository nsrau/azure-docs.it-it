---
title: Concetti relativi al servizio Azure Device Provisioning in hub IoT | Microsoft Docs
description: Descrive i concetti relativi al provisioning del servizio specifici dei dispositivi con il servizio Device Provisioning e l'hub IoT
services: iot-dps
keywords: 
author: nberdy
ms.author: nberdy
ms.date: 10/03/2017
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 96c63e5d0379150ea619dbbe912a21e373f808af
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="iot-hub-device-provisioning-service-concepts"></a>Concetti relativi al servizio Device Provisioning in hub IoT

Il servizio Device Provisioning in hub IoT è un servizio helper per l'hub IoT che consente di configurare il provisioning completamente automatico dei dispositivi in un hub IoT specifico. Con il servizio Device Provisioning è possibile effettuare il provisioning di milioni di dispositivi in modo sicuro e scalabile.

Il provisioning dei dispositivi è un processo due in parti. La prima parte consiste nello stabilire la connessione iniziale tra il dispositivo e la soluzione IoT *registrando* il dispositivo. La seconda parte consiste nell'applicare la *configurazione* corretta al dispositivo in base ai requisiti specifici della soluzione. Una volta completati entrambi questi passaggi, il *provisioning* del dispositivo può considerarsi completato. Il servizio Device Provisioning consente di automatizzare entrambi i passaggi per semplificare l'esperienza di provisioning dei dispositivi.

Questo articolo fornisce una panoramica dei concetti relativi al provisioning in relazione alla gestione del *servizio*. L'articolo è di particolare interesse per le persone coinvolte nella [fase di impostazione cloud](about-iot-dps.md#cloud-setup-step) in preparazione alla distribuzione di un dispositivo.

## <a name="service-operations-endpoint"></a>Endpoint delle operazioni del servizio

L'endpoint delle operazioni del servizio è l'endpoint per la gestione delle impostazioni del servizio e la manutenzione dell'elenco di registrazione. Questo endpoint viene usato solo dall'amministratore del servizio e non dai dispositivi.

## <a name="device-provisioning-endpoint"></a>Endpoint di provisioning dei dispositivi

L'endpoint di provisioning dei dispositivi è l'endpoint centrale con cui tutti i dispositivi comunicano per il provisioning. L'URL è lo stesso per tutti i servizi di provisioning, per eliminare la necessità di sovrascrivere la memoria dei dispositivi con nuove informazioni di connessione in scenari di supply chain. L'[ambito ID](#id-scope) garantisce l'isolamento dei tenant.

## <a name="linked-iot-hubs"></a>Hub IoT collegati

Il servizio Device Provisioning può effettuare il provisioning dei dispositivi solo negli hub IoT che sono stati collegati. Il collegamento di un hub IoT al servizio Device Provisioning fornisce al servizio le autorizzazioni di lettura/scrittura nel registro dei dispositivi dell'hub IoT. Con il collegamento, il servizio Device Provisioning può registrare un ID dispositivo e impostare la configurazione iniziale nel dispositivo gemello. Gli hub IoT collegati possono trovarsi in qualsiasi area di Azure. È possibile collegare hub di altre sottoscrizioni al servizio di provisioning.

## <a name="allocation-policy"></a>Criteri di allocazione

Impostazione a livello di servizio che determina in che modo il servizio Device Provisioning assegna i dispositivi a un hub IoT. Ci sono tre criteri di allocazione supportati:
* **Distribuzione ponderata uniforme**: gli hub IoT collegati hanno le stesse probabilità di essere scelti per il provisioning dei dispositivi. Si tratta dell'impostazione predefinita. Se si effettua il provisioning dei dispositivi in un solo hub IoT, è possibile mantenere questa impostazione.
* **Latenza minima**: il provisioning dei dispositivi viene effettuato in un hub IoT con latenza minima per il dispositivo. Se più hub IoT collegati forniscono la stessa latenza minima, il servizio di provisioning suddivide i dispositivi tra gli hub
* **Configurazione statica tramite l'elenco di registrazione**: la specifica dell'hub IoT desiderato nell'elenco di registrazione ha la priorità rispetto ai criteri di allocazione a livello di servizio.

## <a name="enrollment"></a>Registrazione

Una registrazione è un record di dispositivi o gruppi di dispositivi che a un certo punto eseguono la registrazione. Il record di registrazione contiene le informazioni sul dispositivo o sul gruppo di dispositivi, tra cui il metodo di attestazione per i dispositivi e, facoltativamente, la configurazione iniziale desiderata, l'hub IoT desiderato e l'ID dispositivo desiderato. Il servizio Device Provisioning supporta due tipi di registrazione.

### <a name="enrollment-group"></a>Gruppo di registrazione

Un gruppo di registrazione è un gruppo di dispositivi che condividono un meccanismo di attestazione specifico. Tutti i dispositivi nel gruppo di registrazione presentano certificati X.509 che sono stati firmati dalla stessa CA radice. I gruppi di registrazione possono usare solo il meccanismo di attestazione X.509. Il nome del gruppo di registrazione e il nome di certificato devono essere alfanumerici, in caratteri minuscoli e possono contenere segni meno.

> [!TIP]
> È consigliabile usare un gruppo di registrazione per un numero elevato di dispositivi che condividono una configurazione iniziale desiderata o per i dispositivi destinati allo stesso tenant.

### <a name="individual-enrollment"></a>Registrazione individuale

Una registrazione individuale è una voce per un singolo dispositivo che esegue la registrazione. Le registrazioni individuali possono usare certificati X.509 o token di firma di accesso condiviso (in un TPM reale o virtuale) come meccanismo di attestazione. L'ID di registrazione in una singola registrazione è alfanumerico, con caratteri minuscoli e può contenere segni meno. In caso di registrazione individuale è possibile specificare l'ID dispositivo hub IoT desiderato.

> [!TIP]
> È consigliabile usare le registrazioni individuali per i dispositivi che richiedono configurazioni iniziali univoche oppure per i dispositivi che possono usare solo token di firma di accesso condiviso tramite TPM o TPM virtuale come meccanismo di attestazione.

## <a name="registration"></a>Registrazione

Una registrazione è il record di un dispositivo che esegue correttamente la registrazione/il provisioning in un hub IoT tramite il servizio Device Provisioning. I record di registrazione vengono creati automaticamente. Possono essere eliminati, ma non aggiornati.

## <a name="operations"></a>Operazioni

Le operazioni rappresentano l'unità di fatturazione del servizio Device Provisioning. Un'operazione si riferisce al corretto completamento di un'istruzione nel servizio. Le operazioni includono le registrazioni e le ripetizioni delle registrazioni dei dispositivi, oltre alle modifiche sul lato del servizio, come l'aggiunta di voci all'elenco di registrazione e l'aggiornamento delle voci dell'elenco di registrazione.
