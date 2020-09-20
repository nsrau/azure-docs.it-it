---
title: Come abilitare la risorsa della soluzione VMware di Azure
description: Informazioni su come inviare una richiesta di supporto per abilitare la risorsa della soluzione VMware di Azure. È anche possibile richiedere altri nodi nel cloud privato della soluzione VMware di Azure esistente.
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 72a7aca97067ce4e9ed0e901e4016c82b3549eb1
ms.sourcegitcommit: 0fd1f3fe7817ad44d878d580ec167e1508051795
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2020
ms.locfileid: "90817897"
---
# <a name="how-to-enable-azure-vmware-solution-resource"></a>Come abilitare la risorsa della soluzione VMware di Azure
Informazioni su come inviare una richiesta di supporto per abilitare la risorsa della soluzione VMware di Azure. È anche possibile richiedere altri nodi nel cloud privato della soluzione VMware di Azure esistente.

## <a name="eligibility-criteria"></a>Criteri di idoneità

* È necessario un [Enterprise Agreement di Azure (EA)](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-agreements) con Microsoft.
* Sarà necessario un account Azure in una sottoscrizione di Azure.


## <a name="enable-azure-vmware-solution-resource"></a>Abilitare la risorsa della soluzione VMware di Azure
Prima di creare la risorsa della soluzione VMware di Azure, è necessario inviare un ticket di supporto per l'allocazione dei nodi. Una volta ricevuta la richiesta, il team di supporto richiede fino a cinque giorni lavorativi per confermare la richiesta e allocare i nodi. Se si dispone di un cloud privato della soluzione VMware di Azure esistente e si vogliono allocare più nodi, si procederà nello stesso processo.


1. Nella portale di Azure in Guida e **supporto tecnico**creare una **[nuova richiesta di supporto](https://rc.portal.azure.com/#create/Microsoft.Support)** e fornire le informazioni seguenti per il ticket:
   - **Tipo di problema:** Tecnici
   - **Sottoscrizione:** Selezionare la sottoscrizione
   - **Servizio:** Tutti i servizi > soluzione VMware di Azure
   - **Risorsa:** Domanda generale 
   - **Riepilogo:** Capacità necessaria
   - **Tipo di problema:** Capacity Management Issues (Problemi di gestione della capacità)
   - **Sottotipo del problema:** Customer Request for Additional Host Quota/Capacity (Richiesta del cliente di capacità/quota host aggiuntiva)

1. Nella **Descrizione** del ticket di supporto, nella scheda **Dettagli** , fornire:

   - POC o produzione 
   - Nome area
   - Numero di nodi
   - Altri dettagli

   >[!NOTE]
   >La soluzione VMware di Azure consiglia un minimo di tre nodi per avviare il cloud privato e per la ridondanza di N + 1 nodi. 

1. Selezionare **Verifica + crea** per inviare la richiesta.

   Per confermare la richiesta, l'addetto al supporto richiederà fino a cinque giorni lavorativi.

   >[!IMPORTANT] 
   >Se si dispone già di una soluzione VMware di Azure esistente e si richiedono nodi aggiuntivi, si noti che per l'allocazione dei nodi sono necessari cinque giorni lavorativi. 

1. Prima di poter effettuare il provisioning dei nodi, assicurarsi di registrare il provider di risorse **Microsoft. AVS** nella portale di Azure.  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   Per informazioni su come registrare il provider di risorse in altri modi, vedere [Provider e tipi di risorse di Azure](../azure-resource-manager/management/resource-providers-and-types.md).