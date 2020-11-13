---
title: Come abilitare la risorsa della soluzione VMware di Azure
description: Informazioni su come inviare una richiesta di supporto per abilitare la risorsa della soluzione VMware di Azure. È anche possibile richiedere altri nodi nel cloud privato della soluzione VMware di Azure esistente.
ms.topic: how-to
ms.date: 11/12/2020
ms.openlocfilehash: 7c805e9e622f55593ff1fbb72a355d233b7e3618
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94576384"
---
# <a name="how-to-enable-azure-vmware-solution-resource"></a>Come abilitare la risorsa della soluzione VMware di Azure
Informazioni su come inviare una richiesta di supporto per abilitare la risorsa della [soluzione VMware di Azure](introduction.md) . È anche possibile richiedere altri nodi nel cloud privato della soluzione VMware di Azure esistente.

## <a name="eligibility-criteria"></a>Criteri di idoneità

Sarà necessario un account Azure in una sottoscrizione di Azure. La sottoscrizione di Azure deve essere conforme a uno dei criteri seguenti:

* Una sottoscrizione in un [Enterprise Agreement di Azure (EA)](../cost-management-billing/manage/ea-portal-agreements.md) con Microsoft.
* Sottoscrizione gestita di Cloud Solution Provider (CSP) in un piano di Azure.


## <a name="enable-azure-vmware-solution-for-ea-customers"></a>Abilitare la soluzione VMware di Azure per i clienti con contratto Enterprise
Prima di creare la risorsa della soluzione Azure VMware, sarà necessario inviare un ticket di supporto per ottenere l'allocazione dei nodi. Dopo la ricezione della richiesta da parte del team di supporto, sono necessari fino a cinque giorni per completare la richiesta e allocare i nodi. Se è disponibile un cloud privato della soluzione Azure VMware ed è necessaria l'allocazione di altri nodi, sarà necessario seguire lo stesso processo.


1. Nella portale di Azure in Guida e **supporto tecnico** creare una **[nuova richiesta di supporto](https://rc.portal.azure.com/#create/Microsoft.Support)** e fornire le informazioni seguenti per il ticket:
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

## <a name="enable-azure-vmware-solution-for-csp-customers"></a>Abilitare la soluzione VMware di Azure per i clienti CSP 

I CSP devono usare il centro per i [partner Microsoft](https://partner.microsoft.com) per abilitare la soluzione VMware di Azure per i clienti. 

1. In **centro** per i partner selezionare **CSP** per accedere all'area **clienti** .

   :::image type="content" source="media/enable-azure-vmware-solution/csp-customers-screen.png" alt-text="Area clienti del centro per i partner Microsoft" lightbox="media/enable-azure-vmware-solution/csp-customers-screen.png":::

1. Selezionare il cliente, quindi selezionare **Aggiungi prodotti**.

   :::image type="content" source="media/enable-azure-vmware-solution/csp-partner-center.png" alt-text="Microsoft Partner Center" lightbox="media/enable-azure-vmware-solution/csp-partner-center.png":::

1. Selezionare **piano di Azure** e quindi selezionare **Aggiungi al carrello**. 

1. Esaminare e completare la configurazione generale della sottoscrizione del piano Azure per il cliente. Per ulteriori informazioni, vedere la documentazione del centro per i [partner Microsoft](https://docs.microsoft.com/partner-center/azure-plan-manage).

Dopo aver configurato il piano di Azure e le autorizzazioni di controllo degli accessi in base al ruolo necessarie sono disponibili come CSP, si utilizzerà una procedura analoga per abilitare la quota per una sottoscrizione di piano di Azure. Una volta aggiunto al piano Azure, il cliente o l'amministratore partner può distribuire un cloud privato della soluzione VMware di Azure tramite il portale di Azure. 

## <a name="next-steps"></a>Passaggi successivi

Dopo aver abilitato la risorsa della soluzione VMware di Azure e aver installato la rete corretta, è possibile [creare un cloud privato](tutorial-create-private-cloud.md).