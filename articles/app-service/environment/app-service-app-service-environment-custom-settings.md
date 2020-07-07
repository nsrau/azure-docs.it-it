---
title: Configurare impostazioni personalizzate
description: Configurare le impostazioni che si applicano all'intero ambiente di Servizio app di Azure. Informazioni su come eseguire questa operazione con modelli di Azure Resource Manager.
author: stefsch
ms.assetid: 1d1d85f3-6cc6-4d57-ae1a-5b37c642d812
ms.topic: tutorial
ms.date: 12/19/2019
ms.author: stefsch
ms.custom: mvc, seodec18
ms.openlocfilehash: 09c41c7480b262e6f1a912ad4b708e485d86bf56
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/01/2020
ms.locfileid: "85833503"
---
# <a name="custom-configuration-settings-for-app-service-environments"></a>Impostazioni di configurazione personalizzate per gli ambienti del servizio app
## <a name="overview"></a>Panoramica
Gli ambienti del servizio app sono specifici di un singolo cliente. Per questo motivo alcune impostazioni di configurazione possono essere applicate esclusivamente ad ambienti del servizio app. Questo articolo descrive le diverse personalizzazioni disponibili per gli ambienti del servizio app.

Se non è disponibile un ambiente del servizio app, vedere [Come creare un ambiente del servizio app](app-service-web-how-to-create-an-app-service-environment.md).

È possibile archiviare le personalizzazioni dell'ambiente del servizio app tramite una matrice nel nuovo attributo **clusterSettings** . Questo attributo si trova nel dizionario "Properties" dell'entità di Azure Resource Manager *hostingEnvironments* .

Il frammento di modello di Resource Manager abbreviato seguente illustra l'attributo **clusterSettings** :

```json
"resources": [
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/hostingEnvironments",
    "name": ...,
    "location": ...,
    "properties": {
        "clusterSettings": [
            {
                "name": "nameOfCustomSetting",
                "value": "valueOfCustomSetting"
            }
        ],
        "workerPools": [ ...],
        etc...
    }
}
```

L'attributo **clusterSettings** può essere incluso in un modello di Resource Manager per aggiornare l'ambiente del servizio app.

## <a name="use-azure-resource-explorer-to-update-an-app-service-environment"></a>Usare Esplora risorse di Azure per aggiornare un ambiente del servizio app
In alternativa, è possibile aggiornare l'ambiente del servizio app tramite [Esplora risorse di Azure](https://resources.azure.com).  

1. In Esplora risorse passare al nodo dell'ambiente del servizio app (**subscriptions** > **resourceGroups** > **providers** > **Microsoft.Web** > **hostingEnvironments**). quindi fare clic sull'ambiente del servizio app specifico che si vuole aggiornare.
2. Nel riquadro a destra fare clic su **Lettura/Scrittura** nella barra degli strumenti superiore per consentire la modifica interattiva in Esplora risorse.  
3. Fare clic sul pulsante **Modifica** blu per rendere modificabile il modello di Resource Manager.
4. Scorrere fino alla fine del riquadro destro. L'attributo **clusterSettings** si trova nella parte inferiore. Qui è possibile immettere o aggiornare il valore corrispondente.
5. Digitare o copiare e incollare la matrice dei valori di configurazione voluti all'interno dell'attributo **clusterSettings** .  
6. Fare clic sul pulsante verde **PUT** situato nella parte superiore del riquadro destro per eseguire il commit della modifica nell'ambiente del servizio app.

Indipendentemente dalla modalità di invio della modifica, perché le modifiche siano effettive occorrono circa 30 minuti per ognuno dei front-end nell'ambiente del servizio.
Ad esempio, se un ambiente del servizio app dispone di quattro front-end, per l'aggiornamento della configurazione occorreranno circa due ore. Durante l'implementazione della modifica della configurazione non è possibile eseguire altre operazioni di ridimensionamento o di modifica della configurazione nell'ambiente del servizio app.

## <a name="enable-internal-encryption"></a>Abilitare la crittografia interna

L'ambiente del servizio app funziona come un sistema di black box in cui non è possibile visualizzare i componenti interni o la comunicazione all'interno del sistema. Per consentire una velocità effettiva più elevata, la crittografia non è abilitata per impostazione predefinita tra i componenti interni. Il sistema è sicuro perché il traffico è completamente inaccessibile per il monitoraggio o l'accesso. Tuttavia, se sono presenti requisiti di conformità che richiedono la crittografia completa del percorso dati dall'inizio alla fine, è possibile abilitare questa funzionalità con l'attributo clusterSetting.  

```json
"clusterSettings": [
    {
        "name": "InternalEncryption",
        "value": "1"
    }
],
```

Dopo aver abilitato l'impostazione InternalEncryption in clusterSetting, è possibile che si verifichino problemi in termini di prestazioni del sistema. Quando si apporta la modifica per abilitare InternalEncryption, l'ambiente del servizio app sarà in uno stato instabile fino a quando la modifica non viene propagata completamente. Il completamento della propagazione completa della modifica può richiedere alcune ore, a seconda del numero di istanze disponibili nell'ambiente del servizio app. È consigliabile non abilitare questa impostazione in un ambiente del servizio app mentre è in uso. Se è necessario abilitare questa impostazione in un ambiente del servizio app usato attivamente, è opportuno deviare il traffico in un ambiente di backup fino al completamento dell'operazione. 

## <a name="disable-tls-10-and-tls-11"></a>Disabilitare TLS 1.1 e TLS 1.0

Per gestire le impostazioni di TLS in ogni app, è quindi possibile usare le indicazioni fornite nella documentazione [Applicare versioni di TLS](../configure-ssl-bindings.md#enforce-tls-versions). 

Per disabilitare tutto il traffico TLS 1.1 e TLS 1.0 in ingresso per tutte le app in un ambiente del servizio app, è possibile impostare la seguente voce **clusterSettings**:

```json
"clusterSettings": [
    {
        "name": "DisableTls1.0",
        "value": "1"
    }
],
```

Il nome dell'impostazione indica 1.0, ma quando è configurata, disabilita sia TLS 1.0 che TLS 1.1.

## <a name="change-tls-cipher-suite-order"></a>Modifica dell'ordine dei pacchetti di crittografia TLS
Un'altra domanda dei clienti riguarda la possibilità di modificare l'elenco delle crittografie negoziate dal server. Questo risultato può essere ottenuto modificando **clusterSettings** come illustrato di seguito. L'elenco dei pacchetti di crittografia può essere recuperato da [questo articolo MSDN](https://msdn.microsoft.com/library/windows/desktop/aa374757\(v=vs.85\).aspx).

```json
"clusterSettings": [
    {
        "name": "FrontEndSSLCipherSuiteOrder",
        "value": "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384_P256,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256"
    }
],
```

> [!WARNING]
> Se per il pacchetto di crittografia vengono impostati valori non corretti che non possono essere riconosciuti da SChannel, tutta la comunicazione TLS con il server potrebbe non funzionare. In tal caso, sarà necessario rimuovere la voce *FrontEndSSLCipherSuiteOrder* da **clusterSettings** e inviare il modello di Resource Manager aggiornato per ripristinare le impostazioni predefinite del pacchetto di crittografia.  Usare questa funzionalità con cautela.

## <a name="get-started"></a>Introduzione
Il sito dei modelli di avvio rapido di Azure Resource Manager include un modello con la definizione di base per la [creazione di un ambiente del servizio app](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/).

<!-- LINKS -->

<!-- IMAGES -->
