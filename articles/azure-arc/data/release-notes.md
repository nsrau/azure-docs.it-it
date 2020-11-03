---
title: Azure Arc Enabled Data Services-Note sulla versione
description: Note sulla versione più recenti
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 10/29/2020
ms.topic: conceptual
ms.openlocfilehash: e7312ffd4d55f0403359f8aad2d0a8433a716f77
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93280379"
---
# <a name="release-notes---azure-arc-enabled-data-services-preview"></a>Note sulla versione-Azure Arc Enabled Data Services (anteprima)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="october-2020"></a>Ottobre 2020 

### <a name="breaking-changes"></a>Modifiche di rilievo

Questa versione introduce le modifiche di rilievo seguenti: 

* I file di definizione di risorsa personalizzata PostgreSQL (CRD) sostituiscono il termine `shards` che viene rinominato in `workers` . Questo termine ( `workers` ) corrisponde al nome del parametro della riga di comando.

* `azdata arc postgres server delete` richiede la conferma prima di eliminare un'istanza di postgres.  Usare `--force` per ignorare la richiesta.

### <a name="additional-changes"></a>Modifiche aggiuntive

* È stato aggiunto un nuovo parametro facoltativo a `azdata arc postgres server create` chiamato `--volume-claim mounts` . Il valore è un elenco delimitato da virgole di montaggi di attestazioni del volume. Un montaggio di attestazioni volume è una coppia di tipo di volume e il nome del PVC. Il tipo di volume per ora consente solo `backup` .  In PostgreSQL, quando il tipo di volume è `backup` , il PVC viene montato in `/mnt/db-backups` .  Ciò consente di condividere i backup tra le istanze Postgres, in modo che il backup di un'istanza di Postgres possa essere ripristinato in un altro.

* Nuovi nomi brevi per le definizioni di risorse personalizzate PostgreSQL: 

  * `pg11` 

  * `pg12`

* Il caricamento dei dati di telemetria da fornisce all'utente un:

   * Numero di punti caricati in Azure

     o 

   * Se non sono stati caricati dati in Azure, verrà richiesto di riprovare.

* `azdata arc dc debug copy-logs` ora legge anche dalla `/var/opt/controller/log` cartella e raccoglie i log postgres.

*   Visualizza un indicatore funzionante durante la creazione e il ripristino del backup in postgres.

* `azdata arc postrgres backup list` include ora le informazioni sulle dimensioni del backup.

* La proprietà nome amministratore di SQL Istanza gestita è stata aggiunta alla colonna destra del pannello panoramica nel portale di Azure.



## <a name="september-2020"></a>Settembre 2020

Azure Arc Enabled Data Services è stato rilasciato per l'anteprima pubblica. I servizi dati abilitati per Arc consentono di gestire servizi dati ovunque.

- Istanza gestita di SQL
- PostgreSQL Hyperscale

Per istruzioni, vedere [quali sono i servizi dati abilitati per Azure Arc?](overview.md)

## <a name="known-limitations-and-issues"></a>Limitazioni e problemi noti

- I nomi delle istanze gestite di SQL non possono contenere più di 13 caratteri
- Nessun aggiornamento sul posto per il controller dati di Azure Arc o le istanze di database.
- Le immagini del contenitore di servizi dati abilitati per Arc non sono firmate.  Potrebbe essere necessario configurare i nodi di Kubernetes per consentire il pull delle immagini del contenitore senza firma.  Se ad esempio si usa Docker come runtime del contenitore, è possibile impostare la variabile di ambiente DOCKER_CONTENT_TRUST = 0 e riavviare.  Altri runtime del contenitore hanno opzioni simili, ad esempio in [OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration).
- Non è possibile creare istanze gestite di SQL Azure Arc abilitate o gruppi di server con iperscalabilità PostgreSQL dalla portale di Azure.
- Per il momento, se si usa NFS, è necessario impostare `allowRunAsRoot` su `true` nel file del profilo di distribuzione prima di creare il controller dati di Azure Arc.
- Solo autenticazione dell'account di accesso di SQL e PostgreSQL.  Nessun supporto per Azure Active Directory o Active Directory.
- Per la creazione di un controller di dati in OpenShift sono necessari vincoli di sicurezza rilassati.  Per informazioni dettagliate, vedere la documentazione.
- Il ridimensionamento del numero _di nodi del_ ruolo di lavoro con iperscalabilità di PostgreSQL non è supportato.
- Se si usa il motore del servizio Kubernetes di Azure (motore AKS) nell'hub Azure Stack con il controller di dati di Azure Arc e le istanze di database, l'aggiornamento a una versione di Kubernetes più recente non è supportato. Disinstallare Azure Arc data controller e tutte le istanze di database prima di aggiornare il cluster Kubernetes.
- L'anteprima non supporta il backup/ripristino per il motore Postgres versione 11. Supporta solo backup/ripristino per Postgres versione 12.
- Azure Kubernetes Service (AKS), i cluster che si estendono su [più zone di disponibilità](../../aks/availability-zones.md) non sono attualmente supportati per Azure Arc Enabled Data Services. Per evitare questo problema, quando si crea il cluster AKS in portale di Azure, se si seleziona un'area in cui sono disponibili le zone, deselezionare tutte le zone dal controllo di selezione. Vedere l'immagine seguente:

   :::image type="content" source="media/release-notes/aks-zone-selector.png" alt-text="Deselezionare le caselle di controllo per ogni zona per specificare nessuna.":::

## <a name="next-steps"></a>Passaggi successivi
  
> **Si desidera fare semplicemente una prova?**  
> È possibile iniziare rapidamente a usare [Azure Arc Jumpstart](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) nel servizio Azure Kubernetes, in AWS Elastic Kubernetes Service (EKS), in Google Cloud Kubernetes Engine (GKE) o in una macchina virtuale di Azure.

[Installare gli strumenti client](install-client-tools.md)

[Creare il controller dati di Azure Arc](create-data-controller.md) (è prima necessario installare gli strumenti client)

[Creare un'istanza gestita di SQL di Azure in Azure Arc](create-sql-managed-instance.md) (è prima necessario creare un controller dati di Azure Arc)

[Creare un gruppo di server di Database di Azure per PostgreSQL Hyperscale in Azure Arc](create-postgresql-hyperscale-server-group.md) (è prima necessario creare un controller dati di Azure Arc)
