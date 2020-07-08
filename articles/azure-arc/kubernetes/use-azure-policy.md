---
title: Usare Criteri di Azure per applicare le configurazioni del cluster su larga scala (anteprima)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Usare Criteri di Azure per applicare le configurazioni del cluster su larga scala
keywords: Kubernetes, Arc, Azure, K8s, contenitori
ms.openlocfilehash: 4c013fe562d89bff4d1ce9c9f3e832e1b51c70f1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85341370"
---
# <a name="use-azure-policy-to-apply-cluster-configurations-at-scale-preview"></a>Usare Criteri di Azure per applicare le configurazioni del cluster su larga scala (anteprima)

## <a name="overview"></a>Panoramica

Usare i criteri di Azure per applicare che a ogni `Microsoft.Kubernetes/connectedclusters` risorsa o risorsa abilitata per git-Ops `Microsoft.ContainerService/managedClusters` è stata applicata una specifica `Microsoft.KubernetesConfiguration/sourceControlConfigurations` applicazione. Per usare Criteri di Azure, selezionare una definizione dei criteri esistente e creare un'assegnazione di criteri. Quando si crea l'assegnazione dei criteri, si imposta l'ambito per l'assegnazione: si tratterà di un gruppo di risorse o una sottoscrizione di Azure. È anche possibile impostare i parametri per la `sourceControlConfiguration` da creare. Una volta creata l'assegnazione, il motore dei criteri identificherà tutte le risorse `connectedCluster` o `managedCluster` che si trovano all'interno dell'ambito e applicherà `sourceControlConfiguration` a ciascuna di esse.

Se si usano più repository Git come origini di verità per ogni cluster (ad esempio, un repository per l'operatore IT centrale/cluster e altri repository per i team di applicazioni), è possibile abilitare questa impostazione usando più assegnazioni di criteri, con ciascuna assegnazione di criteri che sarà configurata per l'uso di un repository Git diverso.

## <a name="create-a-policy-assignment"></a>Creare un'assegnazione di criteri

1. Nel portale di Azure passare a Criteri e nella sezione **Creazione** della barra laterale selezionare **Definizioni**.
2. Scegliere il criterio predefinito "Distribuisci GitOps nel cluster Kubernetes" nella categoria "Kubernetes" e fare clic su **assegna**.
3. Impostare l'**Ambito** sul gruppo di gestione, sulla sottoscrizione o sul gruppo di risorse in cui si applicherà l'assegnazione dei criteri.
4. Se si desidera escludere risorse dall'ambito del criterio, impostare **Esclusioni**.
5. Compilare i campi **Nome** e **Descrizione** dell'assegnazione dei criteri per identificarla più facilmente.
6. Verificare che l'**Applicazione dei criteri** sia impostata su *Abilitata*.
7. Selezionare **Avanti**.
8. Impostare i valori dei parametri che verranno usati durante la creazione di `sourceControlConfiguration`.
9. Selezionare **Avanti**.
10. Abilitare **Crea un'attività di correzione**.
11. Assicurarsi che sia selezionata l'opzione **Crea un'identità gestita** e che l'identità disponga di autorizzazioni di **Collaboratore**. Per altre informazioni sulle autorizzazioni necessarie, vedere [questo documento](https://docs.microsoft.com/azure/governance/policy/assign-policy-portal) e [il commento in questo documento](https://docs.microsoft.com/azure/governance/policy/how-to/remediate-resources).
12. Selezionare **Rivedi e crea**.

Dopo aver creato l'assegnazione dei criteri, per qualsiasi nuova risorsa `connectedCluster` (o `managedCluster` con gli agenti GitOps installati) che si trova nell'ambito dell'assegnazione, viene applicato `sourceControlConfiguration`. Per i cluster esistenti, sarà necessario eseguire manualmente un'attività di correzione. Per rendere effettive le assegnazioni di criteri sono necessari in genere 10-20 minuti.

## <a name="verify-a-policy-assignment"></a>Verificare un'assegnazione di criteri

1. Nel portale di Azure passare a una delle risorse di `connectedCluster` e nella sezione **Impostazioni** della barra laterale selezionare **Criteri**. L'esperienza utente per il cluster gestito dal servizio Azure Kubernetes non è ancora implementata, ma lo sarà presto.
2. Nell'elenco dovrebbe essere visualizzata l'assegnazione di criteri creata in precedenza e lo **stato di conformità** sarà *Conforme*.
3. Nella sezione **Impostazioni** della barra laterale selezionare **Configurazioni**.
4. Nell'elenco verranno visualizzato il valore `sourceControlConfiguration` creato dall'assegnazione dei criteri.
5. Usare **kubectl** per interrogare il cluster: verranno visualizzati lo spazio dei nomi e gli artefatti creati da `sourceControlConfiguration`.
6. Entro 5 minuti dovrebbero essere visibile nel cluster gli artefatti descritti nei manifesti nel repository Git configurato.

## <a name="next-steps"></a>Passaggi successivi

* [Configurare Monitoraggio di Azure per i contenitori con cluster Kubernetes abilitati per Arc](./deploy-azure-monitor-for-containers.md)
