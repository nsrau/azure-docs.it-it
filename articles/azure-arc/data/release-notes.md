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
ms.openlocfilehash: 2da8bd0b36b553a4b5f85b6f79987ab1a7b8d5a7
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93286571"
---
# <a name="release-notes---azure-arc-enabled-data-services-preview"></a>Note sulla versione-Azure Arc Enabled Data Services (anteprima)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="october-2020"></a>Ottobre 2020 

Numero di versione dell'interfaccia della riga di comando di Azure ( `azdata` ): 20.2.3. Scarica all'indirizzo [https://aka.ms/azdata](https://aka.ms/azdata) .

### <a name="breaking-changes"></a>Modifiche di rilievo

Questa versione introduce le modifiche di rilievo seguenti: 

* I file di definizione di risorsa personalizzata PostgreSQL (CRD) sostituiscono il termine `shards` che viene rinominato in `workers` . Questo termine ( `workers` ) corrisponde al nome del parametro della riga di comando.

* `azdata arc postgres server delete` richiede la conferma prima di eliminare un'istanza di postgres.  Usare `--force` per ignorare la richiesta.

### <a name="additional-changes"></a>Modifiche aggiuntive

* È stato aggiunto un nuovo parametro facoltativo a `azdata arc postgres server create` chiamato `--volume-claim mounts` . Il valore è un elenco delimitato da virgole di montaggi di attestazioni del volume. Un montaggio di attestazioni volume è una coppia di tipo di volume e il nome del PVC. L'unico tipo di volume attualmente supportato è `backup` .  In PostgreSQL, quando il tipo di volume è `backup` , il PVC viene montato in `/mnt/db-backups` .  Ciò consente di condividere i backup tra le istanze di PostgreSQL in modo che sia possibile ripristinare il backup di un'istanza di PostgreSQL in un'altra istanza.

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

* Azure Data Studio supporta la configurazione del numero di nodi del ruolo di lavoro, della vCore e delle impostazioni di memoria per un gruppo di server. 

* L'anteprima supporta il backup/ripristino per Postgres versione 11 e 12.

## <a name="september-2020"></a>Settembre 2020

Azure Arc Enabled Data Services è stato rilasciato per l'anteprima pubblica. I servizi dati abilitati per Arc consentono di gestire servizi dati ovunque.

- Istanza gestita di SQL
- PostgreSQL Hyperscale

Per istruzioni, vedere [quali sono i servizi dati abilitati per Azure Arc?](overview.md)

## <a name="known-limitations-and-issues"></a>Limitazioni e problemi noti

- I nomi di istanza non possono contenere più di 13 caratteri
- Nessun aggiornamento sul posto per il controller dati di Azure Arc o le istanze di database.
- Le immagini del contenitore di servizi dati abilitati per Arc non sono firmate.  Potrebbe essere necessario configurare i nodi di Kubernetes per consentire il pull delle immagini del contenitore senza firma.  Se ad esempio si usa Docker come runtime del contenitore, è possibile impostare la variabile di ambiente DOCKER_CONTENT_TRUST = 0 e riavviare.  Altri runtime del contenitore hanno opzioni simili, ad esempio in [OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration).
- Non è possibile creare istanze gestite di SQL Azure Arc abilitate o gruppi di server con iperscalabilità PostgreSQL dalla portale di Azure.
- Per il momento, se si usa NFS, è necessario impostare `allowRunAsRoot` su `true` nel file del profilo di distribuzione prima di creare il controller dati di Azure Arc.
- Solo autenticazione dell'account di accesso di SQL e PostgreSQL.  Nessun supporto per Azure Active Directory o Active Directory.
- Per la creazione di un controller di dati in OpenShift sono necessari vincoli di sicurezza rilassati.  Per informazioni dettagliate, vedere la documentazione.
- Il ridimensionamento del numero _di nodi del_ ruolo di lavoro con iperscalabilità di PostgreSQL non è supportato.
- Se si usa il motore del servizio Kubernetes di Azure (motore AKS) nell'hub Azure Stack con il controller di dati di Azure Arc e le istanze di database, l'aggiornamento a una versione di Kubernetes più recente non è supportato. Disinstallare Azure Arc data controller e tutte le istanze di database prima di aggiornare il cluster Kubernetes.
- L'anteprima non supporta il backup/ripristino per il motore Postgres versione 11. (Risolto nel 2020 ottobre) Supporta solo backup/ripristino per Postgres versione 12.
- Azure Kubernetes Service (AKS), i cluster che si estendono su [più zone di disponibilità](../../aks/availability-zones.md) non sono attualmente supportati per Azure Arc Enabled Data Services. Per evitare questo problema, quando si crea il cluster AKS in portale di Azure, se si seleziona un'area in cui sono disponibili le zone, deselezionare tutte le zone dal controllo di selezione. Vedere l'immagine seguente:

   :::image type="content" source="media/release-notes/aks-zone-selector.png" alt-text="Deselezionare le caselle di controllo per ogni zona per specificare nessuna.":::


### <a name="known-issues-for-azure-arc-enabled-postgresql-hyperscale"></a>Problemi noti per l'iperscalabilità di PostgreSQL abilitata per Azure Arc   

- La ricreazione di un gruppo di server con il nome di un gruppo di server appena eliminato potrebbe non riuscire o bloccarsi. 
   - **Soluzione alternativa** Non riutilizzare lo stesso nome quando si ricrea un gruppo di server o si attende il servizio di bilanciamento del carico/servizio esterno del gruppo di server eliminato in precedenza. Supponendo che il nome del gruppo di server eliminato sia stato `postgres01` ospitato in uno spazio dei nomi `arc` , prima di ricreare un gruppo di server con lo stesso nome, attendere finché `postgres01-external-svc` non viene visualizzato nell'output del comando kubectl `kubectl get svc -n arc` .
 
- Il caricamento della pagina di panoramica e della pagina di configurazione di calcolo e archiviazione in Azure Data Studio è lento. 



## <a name="next-steps"></a>Passaggi successivi
  
> **Si desidera fare semplicemente una prova?**  
> È possibile iniziare rapidamente a usare [Azure Arc Jumpstart](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) nel servizio Azure Kubernetes, in AWS Elastic Kubernetes Service (EKS), in Google Cloud Kubernetes Engine (GKE) o in una macchina virtuale di Azure.

[Installare gli strumenti client](install-client-tools.md)

[Creare il controller dati di Azure Arc](create-data-controller.md) (è prima necessario installare gli strumenti client)

[Creare un'istanza gestita di SQL di Azure in Azure Arc](create-sql-managed-instance.md) (è prima necessario creare un controller dati di Azure Arc)

[Creare un gruppo di server di Database di Azure per PostgreSQL Hyperscale in Azure Arc](create-postgresql-hyperscale-server-group.md) (è prima necessario creare un controller dati di Azure Arc)
