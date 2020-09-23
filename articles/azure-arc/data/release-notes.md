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
ms.openlocfilehash: 5908083be4e6ed389b606754ffef41a4a371c3e3
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90938725"
---
# <a name="release-notes---azure-arc-enabled-data-services-preview"></a>Note sulla versione-Azure Arc Enabled Data Services (anteprima)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="september-2020"></a>Settembre 2020

Azure Arc Enabled Data Services è stato rilasciato per l'anteprima pubblica. I servizi dati abilitati per Arc consentono di gestire servizi dati ovunque.

- Istanza gestita di SQL
- PostgreSQL Hyperscale

Per istruzioni, vedere [quali sono i servizi dati abilitati per Azure Arc?](overview.md)

## <a name="next-steps"></a>Passaggi successivi

> **Si desidera fare semplicemente una prova?**  
> Inizia rapidamente a usare [Azure Arc Jumpstart](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) in Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) o in una VM di Azure.

[Installare gli strumenti client](install-client-tools.md)

[Creare il controller dati di Azure Arc](create-data-controller.md) (richiede prima di tutto l'installazione degli strumenti client)

[Creare un'istanza gestita di SQL di Azure in Azure Arc (per](create-sql-managed-instance.md) prima cosa è necessaria la creazione di un controller di dati di Azure Arc)

[Creare un gruppo di server per l'iperscalabilità di database di Azure per PostgreSQL in Azure Arc](create-postgresql-hyperscale-server-group.md) (è necessario creare prima di tutto un controller dati di Azure Arc)

## <a name="known-limitations-and-issues"></a>Limitazioni e problemi noti

- I nomi delle istanze gestite di SQL non possono contenere più di 13 caratteri
- Nessun aggiornamento sul posto per il controller dati di Azure Arc o le istanze di database.
- Le immagini del contenitore di servizi dati abilitati per Arc non sono firmate.  Potrebbe essere necessario configurare i nodi Kubernetes per consentire il pull delle immagini del contenitore senza segno.  Se ad esempio si usa Docker come runtime del contenitore, è possibile impostare la variabile di ambiente DOCKER_CONTENT_TRUST = 0 e riavviare.  Altri runtime del contenitore hanno opzioni simili, ad esempio in [OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration).
- Non è possibile creare istanze gestite di SQL Azure Arc abilitate o gruppi di server con iperscalabilità PostgreSQL dalla portale di Azure.
- Per il momento, se si usa NFS, è necessario impostare allowRunAsRoot su true nel file del profilo di distribuzione prima di creare il controller di dati di Azure Arc.
- Solo autenticazione dell'account di accesso di SQL e PostgreSQL.  Nessun supporto per Azure Active Directory o Active Directory.
- Per la creazione di un controller di dati in OpenShift sono necessari vincoli di sicurezza rilassati.  Per informazioni dettagliate, vedere la documentazione.
- Il ridimensionamento del numero _di nodi del_ ruolo di lavoro iperscalare Postgres non è supportato.
- Se si usa il motore del servizio Kubernetes di Azure (motore AKS) nell'hub Azure Stack con il controller di dati di Azure Arc e le istanze di database, l'aggiornamento a una versione di Kubernetes più recente non è supportato. Disinstallare Azure Arc data controller e tutte le istanze di database prima di aggiornare il cluster Kubernetes.
- L'anteprima non supporta il backup/ripristino per il motore Postgres versione 11. Supporta solo backup/ripristino per Postgres versione 12.
