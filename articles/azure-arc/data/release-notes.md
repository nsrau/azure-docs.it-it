---
title: Azure Arc Enabled Data Services-Note sulla versione
description: Note sulla versione più recenti
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: d22976254cc804ca53060fb284abde8e80a684e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91319728"
---
# <a name="release-notes---azure-arc-enabled-data-services-preview"></a>Note sulla versione-Azure Arc Enabled Data Services (anteprima)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="september-2020"></a>Settembre 2020

Azure Arc Enabled Data Services è stato rilasciato per l'anteprima pubblica. I servizi dati abilitati per Arc consentono di gestire servizi dati ovunque.

- Istanza gestita di SQL
- PostgreSQL Hyperscale

Per istruzioni, vedere [quali sono i servizi dati abilitati per Azure Arc?](overview.md)

### <a name="known-issues"></a>Problemi noti

I problemi seguenti si applicano a questa versione:

* **Eliminazione del gruppo di server di iperscala PostgreSQL**: se è stata modificata la configurazione del gruppo di server o dell'istanza, attendere il completamento dell'operazione di modifica prima di eliminare un gruppo di server di scalabilità PostgreSQL.

* ** `azdata notebook run` potrebbe non riuscire**: per aggirare questo problema, eseguire `azdata notebook run` in un ambiente virtuale Python. Questo problema si manifesta anche in un tentativo non riuscito di creare un gruppo di server di istanza gestita di SQL o PostgreSQL con la distribuzione guidata Azure Data Studio. In questo caso, è possibile aprire il notebook e fare clic sul pulsante **Esegui tutto** nella parte superiore del notebook.

## <a name="next-steps"></a>Passaggi successivi

> **Si desidera fare semplicemente una prova?**  
> È possibile iniziare rapidamente a usare [Azure Arc Jumpstart](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) nel servizio Azure Kubernetes, in AWS Elastic Kubernetes Service (EKS), in Google Cloud Kubernetes Engine (GKE) o in una macchina virtuale di Azure.

[Installare gli strumenti client](install-client-tools.md)

[Creare il controller dati di Azure Arc](create-data-controller.md) (è prima necessario installare gli strumenti client)

[Creare un'istanza gestita di SQL di Azure in Azure Arc](create-sql-managed-instance.md) (è prima necessario creare un controller dati di Azure Arc)

[Creare un gruppo di server di Database di Azure per PostgreSQL Hyperscale in Azure Arc](create-postgresql-hyperscale-server-group.md) (è prima necessario creare un controller dati di Azure Arc)

## <a name="known-limitations-and-issues"></a>Limitazioni e problemi noti

- I nomi delle istanze gestite di SQL non possono contenere più di 13 caratteri
- Nessun aggiornamento sul posto per il controller dati di Azure Arc o le istanze di database.
- Le immagini del contenitore di servizi dati abilitati per Arc non sono firmate.  Potrebbe essere necessario configurare i nodi di Kubernetes per consentire il pull delle immagini del contenitore senza firma.  Se ad esempio si usa Docker come runtime del contenitore, è possibile impostare la variabile di ambiente DOCKER_CONTENT_TRUST = 0 e riavviare.  Altri runtime del contenitore hanno opzioni simili, ad esempio in [OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration).
- Non è possibile creare istanze gestite di SQL Azure Arc abilitate o gruppi di server con iperscalabilità PostgreSQL dalla portale di Azure.
- Per il momento, se si usa NFS, è necessario impostare allowRunAsRoot su true nel file del profilo di distribuzione prima di creare il controller di dati di Azure Arc.
- Solo autenticazione dell'account di accesso di SQL e PostgreSQL.  Nessun supporto per Azure Active Directory o Active Directory.
- Per la creazione di un controller di dati in OpenShift sono necessari vincoli di sicurezza rilassati.  Per informazioni dettagliate, vedere la documentazione.
- Il ridimensionamento del numero _di nodi del_ ruolo di lavoro iperscalare Postgres non è supportato.
- Se si usa il motore del servizio Kubernetes di Azure (motore AKS) nell'hub Azure Stack con il controller di dati di Azure Arc e le istanze di database, l'aggiornamento a una versione di Kubernetes più recente non è supportato. Disinstallare Azure Arc data controller e tutte le istanze di database prima di aggiornare il cluster Kubernetes.
- L'anteprima non supporta il backup/ripristino per il motore Postgres versione 11. Supporta solo backup/ripristino per Postgres versione 12.
