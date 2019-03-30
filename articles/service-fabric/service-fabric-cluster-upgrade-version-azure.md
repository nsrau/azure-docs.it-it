---
title: Aggiornare un cluster di Azure Service Fabric | Documentazione Microsoft
description: Aggiornamento del codice di Service Fabric e/o della configurazione eseguita in un cluster di Service Fabric, con impostazione della modalità di aggiornamento del cluster, aggiornamento dei certificati, aggiunta di porte dell'applicazione, applicazione di patch del sistema operativo e così via. Possibili risultati degli aggiornamenti.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: 15190ace-31ed-491f-a54b-b5ff61e718db
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/12/2018
ms.author: aljo
ms.openlocfilehash: 234bff5049babf0c4b1d036b40201720b2736228
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58661633"
---
# <a name="upgrade-the-service-fabric-version-of-a-cluster"></a>Aggiornare la versione di Service Fabric di un cluster

Per i sistemi attuali la progettazione a livello di aggiornamento è fondamentale per il successo a lungo termine di un prodotto. Un cluster di Azure Service Fabric è una risorsa di proprietà dell'utente parzialmente gestita da Microsoft. Questo articolo descrive come aggiornare la versione di Service Fabric in esecuzione nel cluster di Azure.

È possibile impostare il cluster per ricevere gli aggiornamenti automatici di Fabric quando vengono rilasciati da Microsoft oppure è possibile selezionare una versione di Fabric supportata da eseguire nel cluster.

A questo scopo, impostare l'opzione di configurazione "upgradeMode" del cluster nel portale oppure usare Resource Manager al momento della creazione o successivamente in un cluster attivo. 

> [!NOTE]
> Verificare che il cluster esegua sempre una versione di Fabric supportata. Quando viene annunciato il rilascio di una nuova versione di Service Fabric, viene segnalato il termine del periodo di supporto per la versione precedente dopo un minimo di 60 giorni da tale data. Le nuove versioni vengono annunciate nel [blog del team di Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/). A questo punto è possibile scegliere la nuova versione. 
> 
> 

14 giorni prima della scadenza della versione in esecuzione nel cluster, viene generato un evento di integrità e il cluster passa a uno stato di avviso. Il cluster rimane in stato di avviso fino a quando non viene eseguito l'aggiornamento a una versione di Fabric supportata.

## <a name="set-the-upgrade-mode-in-the-azure-portal"></a>Impostare la modalità di aggiornamento nel portale di Azure
È possibile impostare l'aggiornamento automatico o manuale durante la creazione del cluster.

![Creazione - Modalità manuale][Create_Manualmode]

È possibile impostare l'aggiornamento automatico o manuale in un cluster attivo con l'esperienza di gestione. 

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal"></a>Aggiornamento a una nuova versione in un cluster impostato sulla modalità manuale tramite il portale
Per eseguire l'aggiornamento a una nuova versione, è sufficiente selezionare la versione disponibile nell'elenco a discesa e salvare. L'aggiornamento di Fabric viene avviato automaticamente. Durante l'aggiornamento vengono rispettati i criteri di integrità del cluster (una combinazione dell'integrità del nodo e dell'integrità di tutte le applicazioni in esecuzione nel cluster).

Se i criteri di integrità del cluster non vengono soddisfatti, viene eseguito il rollback dell'aggiornamento. Per altre informazioni su come impostare questi criteri di integrità personalizzati, scorrere questo documento verso il basso. 

Dopo aver risolto i problemi che hanno determinato il rollback, è necessario avviare di nuovo l'aggiornamento, seguendo la stessa procedura precedente.

![Gestione - Modalità automatica][Manage_Automaticmode]

## <a name="set-the-upgrade-mode-using-a-resource-manager-template"></a>Impostare la modalità di aggiornamento tramite un modello di Resource Manager
Aggiungere l'opzione di configurazione "upgradeMode" alla definizione della risorsa Microsoft.ServiceFabric/clusters e impostare "clusterCodeVersion" su una delle versioni di Fabric supportate come illustrato di seguito, quindi distribuire il modello. I valori validi per "upgradeMode" sono "Manual" e "Automatic"

![Modalità di aggiornamento tramite Azure Resource Manager][ARMUpgradeMode]

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-a-resource-manager-template"></a>Aggiornamento a una nuova versione in un cluster impostato sulla modalità manuale tramite un modello di Resource Manager
Quando il cluster è in modalità manuale, per eseguire l'aggiornamento a una nuova versione modificare "clusterCodeVersion" impostando una versione supportata e quindi eseguire la distribuzione. Con la distribuzione del modello, l'aggiornamento di Fabric viene avviato automaticamente. Durante l'aggiornamento vengono rispettati i criteri di integrità del cluster (una combinazione dell'integrità del nodo e dell'integrità di tutte le applicazioni in esecuzione nel cluster).

Se i criteri di integrità del cluster non vengono soddisfatti, viene eseguito il rollback dell'aggiornamento.  

Dopo aver risolto i problemi che hanno determinato il rollback, è necessario avviare di nuovo l'aggiornamento, seguendo la stessa procedura precedente.

## <a name="set-custom-health-polices-for-upgrades"></a>Impostare criteri di integrità personalizzati per gli aggiornamenti
È possibile specificare criteri di integrità personalizzati per l'aggiornamento di Fabric. Se per il cluster è stato impostato l'aggiornamento automatico di Fabric, questi criteri vengono applicati alla [fase 1 degli aggiornamenti automatici di Fabric](service-fabric-cluster-upgrade.md#fabric-upgrade-behavior-during-automatic-upgrades).
Se nel cluster è stato impostato l'aggiornamento manuale di Fabric, questi criteri vengono applicati ogni volta che si seleziona una nuova versione attivando nel sistema l'avvio dell'aggiornamento di Fabric nel cluster. Se non si esegue l'override dei criteri, vengono usati quelli predefiniti.

È possibile specificare criteri di integrità personalizzati o esaminare le impostazioni correnti nel pannello relativo agli aggiornamenti di Fabric, selezionando le impostazioni di aggiornamento avanzate. La figura seguente illustra questo passaggio. 

![Gestire criteri di integrità personalizzati][HealthPolices]

## <a name="list-all-available-versions-for-all-environments-for-a-given-subscription"></a>Elencare tutte le versioni disponibili per tutti gli ambienti per una sottoscrizione specifica
Eseguendo questo comando si otterrà un output simile al seguente.

"supportExpiryUtc" indica la scadenza di una determinata versione. La versione più recente non ha una data valida, ma un valore "9999-12-31T23:59:59.9999999", che indica che la data di scadenza non è ancora stata impostata.

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/locations/{{location}}/clusterVersions?api-version=2016-09-01

Example: https://management.azure.com/subscriptions/1857f442-3bce-4b96-ad95-627f76437a67/providers/Microsoft.ServiceFabric/locations/eastus/clusterVersions?api-version=2016-09-01

Output:
{
                  "value": [
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/5.0.1427.9490",
                      "name": "5.0.1427.9490",
                      "type": "Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.0.1427.9490",
                        "supportExpiryUtc": "2016-11-26T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.0.1427.9490",
                      "name": "5.1.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.1.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.4.1427.9490",
                      "name": "4.4.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "4.4.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Linux"
                      }
                    }
                  ]
                }
```

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come personalizzare alcune [impostazioni dei cluster di Service Fabric](service-fabric-cluster-fabric-settings.md)
* Informazioni su come [aumentare o ridurre le istanze del cluster](service-fabric-cluster-scale-up-down.md)
* Informazioni su come eseguire [aggiornamenti dell'applicazione](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
