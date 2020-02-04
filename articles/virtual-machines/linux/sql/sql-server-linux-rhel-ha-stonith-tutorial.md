---
title: Configurare i gruppi di disponibilità per SQL Server nelle macchine virtuali RHEL in Azure - Macchine virtuali Linux | Microsoft Docs
description: Informazioni su come configurare la disponibilità elevata in un ambiente cluster RHEL e su come configurare STONITH
ms.service: virtual-machines-linux
ms.subservice: ''
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: jroth
ms.date: 01/27/2020
ms.openlocfilehash: e48f41f1e13346f551f1d83f462de697631d1b9a
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76775897"
---
# <a name="tutorial-configure-availability-groups-for-sql-server-on-rhel-virtual-machines-in-azure"></a>Esercitazione: Configurare i gruppi di disponibilità per SQL Server nelle macchine virtuali RHEL in Azure 

> [!NOTE]
> L'esercitazione presentata è disponibile in **anteprima pubblica**. 
>
> In questa esercitazione viene usato SQL Server 2017 con RHEL 7.6, ma è possibile usare SQL Server 2019 in RHEL 7 o RHEL 8 per configurare la disponibilità elevata. I comandi per configurare le risorse del gruppo di disponibilità sono cambiati in RHEL 8 ed è quindi opportuno vedere l'articolo [Creare risorse del gruppo di disponibilità](/sql/linux/sql-server-linux-availability-group-cluster-rhel#create-availability-group-resource) e le risorse di RHEL 8 per altre informazioni sui comandi corretti.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> - Creare un nuovo gruppo di risorse, un set di disponibilità e le macchine virtuali Linux di Azure
> - Abilitare la disponibilità elevata
> - Creare un cluster Pacemaker
> - Configurare un agente di isolamento mediante la creazione di un dispositivo STONITH
> - Installare SQL Server e mssql-tools in RHEL
> - Configurare il gruppo di disponibilità Always On di SQL Server
> - Configurare le risorse del gruppo di disponibilità nel cluster Pacemaker
> - Testare un failover e l'agente di isolamento

Questa esercitazione userà l'interfaccia della riga di comando di Azure per distribuire le risorse in Azure.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Se si preferisce installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è richiesta l'interfaccia della riga di comando di Azure versione 2.0.30 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Se sono presenti più sottoscrizioni, [selezionare la sottoscrizione](/cli/azure/manage-azure-subscriptions-azure-cli) in cui dovranno essere distribuite le risorse.

Usare il comando seguente per creare un gruppo di risorse `<resourceGroupName>` in un'area. Sostituire `<resourceGroupName>` con il nome desiderato. Per questa esercitazione verrà usata l'area `East US 2`. Per altre informazioni, vedere la guida di [Avvio rapido](../quick-create-cli.md) seguente.

```azurecli-interactive
az group create --name <resourceGroupName> --location eastus2
```

## <a name="create-an-availability-set"></a>Creare un set di disponibilità

Il passaggio successivo prevede la creazione di un set di disponibilità. Eseguire il comando seguente in Azure Cloud Shell e sostituire `<resourceGroupName>` con il nome del gruppo di risorse. Scegliere un nome per `<availabilitySetName>`.

```azurecli-interactive
az vm availability-set create \
    --resource-group <resourceGroupName> \
    --name <availabilitySetName> \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

Al termine del comando, verranno restituiti i risultati seguenti:

```output
{
  "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/availabilitySets/<availabilitySetName>",
  "location": "eastus2",
  "name": "<availabilitySetName>",
  "platformFaultDomainCount": 2,
  "platformUpdateDomainCount": 2,
  "proximityPlacementGroup": null,
  "resourceGroup": "<resourceGroupName>",
  "sku": {
    "capacity": null,
    "name": "Aligned",
    "tier": null
  },
  "statuses": null,
  "tags": {},
  "type": "Microsoft.Compute/availabilitySets",
  "virtualMachines": []
}
```

## <a name="create-rhel-vms-inside-the-availability-set"></a>Creare macchine virtuali RHEL all'interno del set di disponibilità

> [!WARNING]
> Se si sceglie un'immagine di RHEL con pagamento in base al consumo (PAYG) e si configura la disponibilità elevata, potrebbe essere necessario registrare la sottoscrizione e pagare così un prezzo doppio, in quanto verranno addebitate la sottoscrizione di Microsoft Azure RHEL per la macchina virtuale e la sottoscrizione per Red Hat. Per altre informazioni, vedere https://access.redhat.com/solutions/2458541.
>
> Per evitare questa "doppia fatturazione", quando si crea la macchina virtuale di Azure usare un'immagine di RHEL con disponibilità elevata. Le immagini offerte come immagini di RHEL con disponibilità elevata sono immagini con pagamento in base al consumo e con il repository a disponibilità elevata preabilitato.

1. Ottenere un elenco di immagini di macchine virtuali che offrono RHEL con la disponibilità elevata:

    ```azurecli-interactive
    az vm image list --all --offer "RHEL-HA"
    ```

    I risultati visualizzati sono simili ai seguenti:

    ```output
    [
            {
              "offer": "RHEL-HA",
              "publisher": "RedHat",
              "sku": "7.4",
              "urn": "RedHat:RHEL-HA:7.4:7.4.2019062021",
              "version": "7.4.2019062021"
            },
            {
              "offer": "RHEL-HA",
              "publisher": "RedHat",
              "sku": "7.5",
              "urn": "RedHat:RHEL-HA:7.5:7.5.2019062021",
              "version": "7.5.2019062021"
            },
            {
              "offer": "RHEL-HA",
              "publisher": "RedHat",
              "sku": "7.6",
              "urn": "RedHat:RHEL-HA:7.6:7.6.2019062019",
              "version": "7.6.2019062019"
            }
    ]
    ```

    Per questa esercitazione, verrà scelta l'immagine `RedHat:RHEL-HA:7.6:7.6.2019062019`.

    > [!IMPORTANT]
    > Per configurare il gruppo di disponibilità, i nomi delle macchine virtuali devono contenere meno di 15 caratteri. Il nome utente non può contenere caratteri maiuscoli e le password devono contenere più di 12 caratteri.

1. Nel set di disponibilità verranno create 3 macchine virtuali. Sostituire quanto segue nel comando seguente:

    - `<resourceGroupName>`
    - `<VM-basename>`
    - `<availabilitySetName>`
    - `<VM-Size>`, ad esempio "Standard_D16_v3"
    - `<username>`
    - `<adminPassword>`

    ```azurecli-interactive
    for i in `seq 1 3`; do
           az vm create \
             --resource-group <resourceGroupName> \
             --name <VM-basename>$i \
             --availability-set <availabilitySetName> \
             --size "<VM-Size>"  \
             --image "RedHat:RHEL-HA:7.6:7.6.2019062019" \
             --admin-username "<username>" \
             --admin-password "<adminPassword>" \
             --authentication-type all \
             --generate-ssh-keys
    done
    ```

Il comando precedente crea le macchine virtuali e crea una rete virtuale predefinita per tali macchine virtuali. Per altre informazioni sulle diverse configurazioni, vedere l'articolo con le informazioni di riferimento su [az vm create](https://docs.microsoft.com/cli/azure/vm).

Al termine del comando per ogni macchina virtuale, si otterrà un risultato simile al seguente:

```output
{
  "fqdns": "",
  "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<VM1>",
  "location": "eastus2",
  "macAddress": "<Some MAC address>",
  "powerState": "VM running",
  "privateIpAddress": "<IP1>",
  "publicIpAddress": "",
  "resourceGroup": "<resourceGroupName>",
  "zones": ""
}
```

> [!IMPORTANT]
> L'immagine predefinita creata con il comando precedente crea un disco del sistema operativo da 32 GB per impostazione predefinita. Con questa installazione predefinita, è tuttavia possibile esaurire lo spazio disponibile. Usare quindi il parametro seguente aggiunto al comando `az vm create` precedente per creare un disco del sistema operativo ad esempio da 128 GB: `--os-disk-size-gb 128`.
>
> Successivamente, [configurare Logical Volume Manager (LVM)](../../../virtual-machines/linux/configure-lvm.md) se è necessario espandere i volumi delle cartelle appropriati per l'installazione.

### <a name="test-connection-to-the-created-vms"></a>Testare la connessione alle macchine virtuali create

Connettersi a VM1 o alle altre macchine virtuali usando il comando seguente in Azure Cloud Shell. Se non si riesce a trovare gli indirizzi IP della macchina virtuale, seguire questa guida di [Avvio rapido in Azure Cloud Shell](../../../cloud-shell/quickstart.md#ssh-into-your-linux-vm).

```azurecli-interactive
ssh <username>@publicipaddress
```

Se la connessione viene stabilita, verrà visualizzato l'output seguente che rappresenta il terminale Linux:

```output
[<username>@<VM1> ~]$
```

Digitare `exit` per uscire dalla sessione SSH.

## <a name="enable-high-availability"></a>Abilitare la disponibilità elevata

> [!IMPORTANT]
> Per completare questa parte dell'esercitazione, è necessario disporre di una sottoscrizione di RHEL e del componente aggiuntivo per la disponibilità elevata. Se si usa un'immagine consigliata nella sezione precedente, non è necessario registrare un'altra sottoscrizione.
 
Connettersi a ogni nodo della macchina virtuale e seguire la guida riportata sotto per abilitare la disponibilità elevata. Per altre informazioni, vedere [Abilitare la sottoscrizione a disponibilità elevata per RHEL](/sql/linux/sql-server-linux-availability-group-cluster-rhel#enable-the-high-availability-subscription-for-rhel).

> [!TIP]
> Sarà più semplice se si apre una sessione SSH contemporaneamente per ogni macchina virtuale, perché i comandi illustrati nell'articolo dovranno essere eseguiti in ogni macchina virtuale.
>
> Se si copiano e incollano più comandi `sudo` e viene richiesta una password, i comandi aggiuntivi non verranno eseguiti. Eseguire ogni comando separatamente.


1. Eseguire i comandi seguenti in ogni macchina virtuale per aprire le porte del firewall Pacemaker:

    ```bash
    sudo firewall-cmd --permanent --add-service=high-availability
    sudo firewall-cmd --reload
    ```

1. Aggiornare e installare i pacchetti Pacemaker in tutti i nodi usando i comandi seguenti:

    > [!NOTE]
    > **nmap** è uno strumento che viene installato come parte di questo blocco di comandi per trovare gli indirizzi IP disponibili nella rete. Non è necessario installare **nmap**, ma sarà utile più avanti in questa esercitazione.

    ```bash
    sudo yum update -y
    sudo yum install -y pacemaker pcs fence-agents-all resource-agents fence-agents-azure-arm nmap
    sudo reboot
    ```

1. Impostare la password per l'utente predefinito creato durante l'installazione dei pacchetti Pacemaker. Usare la stessa password in tutti i nodi.

    ```bash
    sudo passwd hacluster
    ```

1. Usare il comando seguente per aprire il file hosts e impostare la risoluzione dei nomi host. Per altre informazioni su come configurare il file hosts, vedere [Configurare il gruppo di disponibilità](/sql/linux/sql-server-linux-availability-group-configure-ha#prerequisites).

    ```
    sudo vi /etc/hosts
    ```

    Nell'editor **vi** immettere `i` per inserire il testo e, in una riga vuota, aggiungere l'**indirizzo IP privato** della macchina virtuale corrispondente. Aggiungere quindi il nome della macchina virtuale dopo uno spazio accanto all'indirizzo IP. Ogni riga deve contenere una voce separata.

    ```output
    <IP1> <VM1>
    <IP2> <VM2>
    <IP3> <VM3>
    ```

    > [!IMPORTANT]
    > È consigliabile usare l'**indirizzo IP privato** precedente. Se in questa configurazione si usa l'indirizzo IP pubblico, l'installazione avrà esito negativo e non è consigliabile esporre la macchina virtuale alle reti esterne.

    Per uscire dall'editor **vi**, premere prima il tasto **ESC** e quindi immettere il comando `:wq` per eseguire la scrittura del file e uscire.

## <a name="create-the-pacemaker-cluster"></a>Creare il cluster Pacemaker

In questa sezione verrà abilitato e avviato il servizio pcsd e quindi verrà configurato il cluster. Per SQL Server in Linux, le risorse del cluster non vengono create automaticamente. È necessario abilitare e creare manualmente le risorse Pacemaker. Per ulteriori altre, vedere l'articolo sulla [configurazione di un'istanza del cluster di failover per RHEL](/sql/linux/sql-server-linux-shared-disk-cluster-red-hat-7-configure#install-and-configure-pacemaker-on-each-cluster-node)

### <a name="enable-and-start-pcsd-service-and-pacemaker"></a>Abilitare e avviare il servizio pcsd e Pacemaker

1. Eseguire i comandi in tutti i nodi. In questo modo, i nodi potranno essere aggiunti nuovamente al cluster dopo il riavvio.

    ```bash
    sudo systemctl enable pcsd
    sudo systemctl start pcsd
    sudo systemctl enable pacemaker
    ``` 

1. Rimuovere eventuali configurazioni cluster esistenti da tutti i nodi. Eseguire il comando seguente:

    ```bash
    sudo pcs cluster destroy 
    sudo systemctl enable pacemaker 
    ```

1. Nel nodo primario eseguire i comandi seguenti per configurare il cluster.

    - Quando si esegue il comando `pcs cluster auth` per autenticare i nodi del cluster, verrà richiesta una password. Immettere la password per l'utente **hacluster** creato in precedenza.

    ```bash
    sudo pcs cluster auth <VM1> <VM2> <VM3> -u hacluster
    sudo pcs cluster setup --name az-hacluster <VM1> <VM2> <VM3> --token 30000
    sudo pcs cluster start --all
    sudo pcs cluster enable --all
    ```

1. Eseguire il comando seguente per verificare che tutti i nodi siano online.

    ```bash
    sudo pcs status
    ```

    Se tutti i nodi sono online, l'output visualizzato sarà simile al seguente:

    ```output
    Cluster name: az-hacluster
     
    WARNINGS:
    No stonith devices and stonith-enabled is not false
     
    Stack: corosync
    Current DC: <VM2> (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
    Last updated: Fri Aug 23 18:27:57 2019
    Last change: Fri Aug 23 18:27:56 2019 by hacluster via crmd on <VM2>
     
    3 nodes configured
    0 resources configured
     
    Online: [ <VM1> <VM2> <VM3> ]
     
    No resources
     
     
    Daemon Status:
          corosync: active/enabled
          pacemaker: active/enabled
          pcsd: active/enabled
    ```

1. Impostare i voti previsti nel cluster attivo su 3. Questo comando influisce solo sul cluster attivo e non modifica i file di configurazione.

    In tutti i nodi impostare i voti previsti con il comando seguente:

    ```bash
    sudo pcs quorum expected-votes 3
    ```

## <a name="configure-the-fencing-agent"></a>Configurare l'agente di isolamento

Un dispositivo STONITH fornisce un agente di isolamento. Le istruzioni seguenti sono state modificate per questa esercitazione. Per altre informazioni, vedere [Creare un dispositivo STONITH](../../../virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker.md#create-stonith-device).
 
[Controllare la versione dell'agente di isolamento di Azure per assicurarsi che sia aggiornata](../../../virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker.md#cluster-installation). Usare il comando seguente:

```bash
sudo yum info fence-agents-azure-arm
```

Verrà visualizzato un output simile all'esempio seguente.

```output
Loaded plugins: langpacks, product-id, search-disabled-repos, subscription-manager
Installed Packages
Name        : fence-agents-azure-arm
Arch        : x86_64
Version     : 4.2.1
Release     : 11.el7_6.8
Size        : 28 k
Repo        : installed
From repo   : rhel-ha-for-rhel-7-server-eus-rhui-rpms
Summary     : Fence agent for Azure Resource Manager
URL         : https://github.com/ClusterLabs/fence-agents
License     : GPLv2+ and LGPLv2+
Description : The fence-agents-azure-arm package contains a fence agent for Azure instances.
```

### <a name="register-a-new-application-in-azure-active-directory"></a>Creare una nuova applicazione in Azure Active Directory
 
 1. Passare a https://portal.azure.com.
 2. Aprire il [pannello Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties). Passare a Proprietà e annotare l'ID directory. Si tratta di `tenant ID`
 3. Fare clic su [**Registrazioni app**](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)
 4. Fare clic su **Nuova registrazione**
 5. Immettere un valore per **Nome**, ad esempio `<resourceGroupName>-app`, e selezionare **Account solo in questa directory dell'organizzazione**
 6. Per Tipo di applicazione selezionare **Web**, immettere un URL di accesso, ad esempio http://localhost), e fare clic su Aggiungi. L'URL di accesso non viene usato e può essere qualsiasi URL valido
 7. Selezionare **Certificati e segreti**, quindi fare clic su **Nuovo segreto client**
 8. Immettere una descrizione per una nuova chiave (segreto client), selezionare **Non scade mai** e fare clic su **Aggiungi**
 9. Prendere nota del valore del segreto. che viene usato come password per l'entità servizio
10. Selezionare **Panoramica**. Annotare l'ID applicazione. Viene usato come nome utente (ID di accesso nella procedura seguente) dell'entità servizio
 
### <a name="create-a-custom-role-for-the-fence-agent"></a>Creare un ruolo personalizzato per l'agente di isolamento

Seguire l'esercitazione [Creare un ruolo personalizzato per le risorse di Azure tramite l'interfaccia della riga di comando di Azure](../../../role-based-access-control/tutorial-custom-role-cli.md#create-a-custom-role).

Il file JSON dovrebbe essere simile al seguente:

- Sostituire `<username>` con il nome desiderato. In questo modo sarà possibile evitare eventuali duplicati durante la creazione di questa definizione del ruolo.
- Sostituire `<subscriptionId>` con l'ID della sottoscrizione di Azure.

```json
{
  "Name": "Linux Fence Agent Role-<username>",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to power-off and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/powerOff/action",
    "Microsoft.Compute/virtualMachines/start/action"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscriptionId>"
  ]
}
```

Per aggiungere il ruolo, eseguire il comando seguente:

- Sostituire `<filename>` con il nome file.
- Se si esegue il comando da un percorso diverso dalla cartella in cui è stato salvato il file, includere il percorso della cartella del file nel comando.

```bash
az role definition create --role-definition "<filename>.json"
```

Dovrebbe venire visualizzato l'output seguente.

```output
{
  "assignableScopes": [
    "/subscriptions/<subscriptionId>"
  ],
  "description": "Allows to power-off and start virtual machines",
  "id": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/roleDefinitions/<roleNameId>",
  "name": "<roleNameId>",
  "permissions": [
    {
      "actions": [
        "Microsoft.Compute/*/read",
        "Microsoft.Compute/virtualMachines/powerOff/action",
        "Microsoft.Compute/virtualMachines/start/action"
      ],
      "dataActions": [],
      "notActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Linux Fence Agent Role-<username>",
  "roleType": "CustomRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="assign-the-custom-role-to-the-service-principal"></a>Assegnare il ruolo personalizzato all'entità servizio

Assegnare all'entità servizio il ruolo personalizzato `Linux Fence Agent Role-<username>` creato nel passaggio precedente. Non usare più il ruolo Owner.
 
1. Passare a https://portal.azure.com.
2. Aprire il [pannello Tutte le risorse](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAll)
3. Selezionare la macchina virtuale del primo nodo del cluster.
4. Fare clic su **Controllo di accesso (IAM)**
5. Fare clic su **Aggiungi un'assegnazione di ruolo**
6. Selezionare il ruolo `Linux Fence Agent Role-<username>` dall'elenco **Ruolo**
7. Nell'elenco **Seleziona** immettere il nome dell'applicazione creata sopra, `<resourceGroupName>-app`
8. Fare clic su **Save** (Salva).
9. Ripetere la procedura precedente per tutti i nodi del cluster.

### <a name="create-the-stonith-devices"></a>Creare i dispositivi STONITH

Eseguire questo comando nel nodo 1:

- Sostituire `<ApplicationID>` con il valore dell'ID della registrazione dell'applicazione.
- Sostituire `<servicePrincipalPassword>` con il valore del segreto client.
- Sostituire `<resourceGroupName>` con il gruppo di risorse della sottoscrizione usata per questa esercitazione.
- Sostituire `<tenantID>` e `<subscriptionId>` con i valori della sottoscrizione di Azure.

```bash
sudo pcs property set stonith-timeout=900
sudo pcs stonith create rsc_st_azure fence_azure_arm login="<ApplicationID>" passwd="<servicePrincipalPassword>" resourceGroup="<resourceGroupName>" tenantId="<tenantID>" subscriptionId="<subscriptionId>" power_timeout=240 pcmk_reboot_timeout=900
```

Poiché è già stata aggiunta una regola del firewall per consentire il servizio a disponibilità elevata (`--add-service=high-availability`), non è necessario aprire le porte del firewall seguenti in tutti i nodi: 2224, 3121, 21064, 5405. Tuttavia, se si verificano problemi di connessione con la disponibilità elevata, usare il comando seguente per aprire le porte associate alla disponibilità elevata.

> [!TIP]
> È facoltativamente possibile aggiungere contemporaneamente tutte le porte indicate in questa esercitazione per risparmiare tempo. Le porte che devono essere aperte sono illustrate sotto nelle sezioni corrispondenti. Per aggiungere ora tutte le porte, aggiungere le porte aggiuntive: 1433 e 5022.

```bash
sudo firewall-cmd --zone=public --add-port=2224/tcp --add-port=3121/tcp --add-port=21064/tcp --add-port=5405/tcp --permanent
sudo firewall-cmd --reload
```

## <a name="install-sql-server-and-mssql-tools"></a>Installare SQL Server e mssql-tools
 
Vedere la sezione seguente per installare SQL Server e mssql-tools nelle macchine virtuali. Eseguire ogni azione in tutti i nodi. Per altre informazioni, vedere [Installare SQL Server in una macchina virtuale Red Hat](/sql/linux/quickstart-install-connect-red-hat).

### <a name="installing-sql-server-on-the-vms"></a>Installazione di SQL Server nelle macchine virtuali

Per installare SQL Server verranno usati i comandi seguenti:

```bash
sudo curl -o /etc/yum.repos.d/mssql-server.repo https://packages.microsoft.com/config/rhel/7/mssql-server-2017.repo
sudo yum install -y mssql-server
sudo /opt/mssql/bin/mssql-conf setup
sudo yum install mssql-server-ha
```

### <a name="open-firewall-port-1433-for-remote-connections"></a>Aprire la porta del firewall 1433 per le connessioni remote

Per connettersi in modalità remota, è necessario aprire la porta 1433 nella macchina virtuale. Usare i comandi seguenti per aprire la porta 1433 nel firewall di ogni macchina virtuale:

```bash
sudo firewall-cmd --zone=public --add-port=1433/tcp --permanent
sudo firewall-cmd --reload
```

### <a name="installing-sql-server-command-line-tools"></a>Installazione degli strumenti da riga di comando di SQL Server

Per installare gli strumenti da riga di comando di SQL Server verranno usati i comandi seguenti. Per altre informazioni, vedere [Installare gli strumenti da riga di comando di SQL Server](/sql/linux/quickstart-install-connect-red-hat#tools).

```bash
sudo curl -o /etc/yum.repos.d/msprod.repo https://packages.microsoft.com/config/rhel/7/prod.repo
sudo yum install -y mssql-tools unixODBC-devel
```
 
> [!NOTE] 
> Per praticità, aggiungere /opt/mssql-tools/bin/ alla variabile di ambiente PATH. In questo modo è possibile eseguire gli strumenti senza specificare il percorso completo. Eseguire i comandi seguenti per modificare la variabile PATH sia per le sessioni di accesso che per le sessioni interattive/non di accesso:</br></br>
`echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile`</br>
`echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc`</br>
`source ~/.bashrc`


### <a name="check-the-status-of-the-sql-server"></a>Verificare lo stato di SQL Server

Al termine della configurazione, è possibile controllare lo stato di SQL Server e verificare che sia in esecuzione:

```bash
systemctl status mssql-server --no-pager
```

Dovrebbe venire visualizzato l'output seguente.

```output
● mssql-server.service - Microsoft SQL Server Database Engine
   Loaded: loaded (/usr/lib/systemd/system/mssql-server.service; enabled; vendor preset: disabled)
   Active: active (running) since Thu 2019-12-05 17:30:55 UTC; 20min ago
     Docs: https://docs.microsoft.com/en-us/sql/linux
 Main PID: 11612 (sqlservr)
   CGroup: /system.slice/mssql-server.service
           ├─11612 /opt/mssql/bin/sqlservr
           └─11640 /opt/mssql/bin/sqlservr
```

## <a name="configure-sql-server-always-on-availability-group"></a>Configurare il gruppo di disponibilità Always On di SQL Server

Per configurare un gruppo di disponibilità Always On di SQL Server per le macchine virtuali, seguire questa procedura. Per altre informazioni, vedere [Configurare un gruppo di disponibilità Always On di SQL Server per la disponibilità elevata in Linux](/sql/linux/sql-server-linux-availability-group-configure-ha)

### <a name="enable-alwayson-availability-groups-and-restart-mssql-server"></a>Abilitare la funzionalità Gruppi di disponibilità Always On e riavviare mssql-server

Abilitare i gruppi di disponibilità AlwaysOn in ogni nodo che ospita un'istanza di SQL Server. Riavviare quindi mssql-server. Eseguire lo script riportato di seguito:

```
sudo /opt/mssql/bin/mssql-conf set hadr.hadrenabled 1
sudo systemctl restart mssql-server
```

### <a name="create-a-certificate"></a>Creare un certificato

L'autenticazione di Active Directory per l'endpoint del gruppo di disponibilità non è attualmente supportata. È quindi necessario usare un certificato per la crittografia dell'endpoint del gruppo di disponibilità.

1. Connettersi a **tutti i nodi** con SQL Server Management Studio (SSMS) o SQL CMD. Eseguire i comandi seguenti per abilitare la sessione AlwaysOn_health e creare una chiave master:

    > [!IMPORTANT]
    > Se ci si connette all'istanza di SQL Server in modalità remota, sarà necessario aprire la porta 1433 sul firewall. Sarà inoltre necessario consentire le connessioni in ingresso alla porta 1433 nel gruppo di sicurezza di rete per ogni macchina virtuale. Per altre informazioni, vedere [Creare una regola di sicurezza](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) per la creazione di una regola di sicurezza in ingresso.

    - Sostituire `<Master_Key_Password>` con la propria password.


    ```sql
    ALTER EVENT SESSION  AlwaysOn_health ON SERVER WITH (STARTUP_STATE=ON);
    GO
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Master_Key_Password>';
    ```

 
1. Connettersi alla replica primaria usando SSMS o SQL CMD. I comandi seguenti creeranno un certificato in `/var/opt/mssql/data/dbm_certificate.cer` e una chiave privata in `var/opt/mssql/data/dbm_certificate.pvk` nella replica primaria di SQL Server:

    - Sostituire `<Private_Key_Password>` con la propria password.

```sql
CREATE CERTIFICATE dbm_certificate WITH SUBJECT = 'dbm';
GO
BACKUP CERTIFICATE dbm_certificate
   TO FILE = '/var/opt/mssql/data/dbm_certificate.cer'
   WITH PRIVATE KEY (
           FILE = '/var/opt/mssql/data/dbm_certificate.pvk',
           ENCRYPTION BY PASSWORD = '<Private_Key_Password>'
       );
```

Per uscire dalla sessione SQL CMD eseguire il comando `exit` e tornare alla sessione SSH.
 
### <a name="copy-the-certificate-to-the-secondary-replicas-and-create-the-certificates-on-the-server"></a>Copiare il certificato nelle repliche secondarie e creare i certificati nel server

1. Copiare i due file creati nello stesso percorso in tutti i server che ospiteranno le repliche di disponibilità.
 
    Nel server primario eseguire il comando `scp` per copiare il certificato nei server di destinazione:

    - Sostituire `<username>` e `<VM2>` con il nome utente e il nome della macchina virtuale di destinazione in uso.
    - Eseguire questo comando per tutte le repliche secondarie.

    > [!NOTE]
    > Non è necessario eseguire `sudo -i`, che restituisce l'ambiente radice. È semplicemente possibile eseguire il comando `sudo` prima di ogni comando come già fatto in precedenza in questa esercitazione.

    ```bash
    # The below command allows you to run commands in the root environment
    sudo -i
    ```

    ```bash
    scp /var/opt/mssql/data/dbm_certificate.* <username>@<VM2>:/home/<username>
    ```

1. Nel server di destinazione eseguire questo comando:

    - Sostituire `<username>` con il nome utente.
    - Il comando `mv` sposta i file o la directory da una posizione a un'altra.
    - Il comando `chown` viene usato per modificare il proprietario e il gruppo di file, directory o collegamenti.
    - Eseguire questi comandi per tutte le repliche secondarie.

    ```bash
    sudo -i
    mv /home/<username>/dbm_certificate.* /var/opt/mssql/data/
    cd /var/opt/mssql/data
    chown mssql:mssql dbm_certificate.*
    ```

1. Lo script Transact-SQL seguente crea un certificato dal backup creato nella replica primaria di SQL Server. Aggiornare lo script con password complesse. La password di decrittografia è la stessa password usata per creare il file PVK nel passaggio precedente. Per creare il certificato, eseguire lo script seguente in tutti i server secondari usando SQL CMD o SSMS:

    ```sql
    CREATE CERTIFICATE dbm_certificate
        FROM FILE = '/var/opt/mssql/data/dbm_certificate.cer'
        WITH PRIVATE KEY (
        FILE = '/var/opt/mssql/data/dbm_certificate.pvk',
        DECRYPTION BY PASSWORD = '<Private_Key_Password>'
                );
    ```

### <a name="create-the-database-mirroring-endpoints-on-all-replicas"></a>Creare endpoint di mirroring del database in tutte le repliche

Eseguire lo script seguente in tutte le istanze di SQL usando SQL CMD o SSMS:

```sql
CREATE ENDPOINT [Hadr_endpoint]
    AS TCP (LISTENER_PORT = 5022)
    FOR DATABASE_MIRRORING (
    ROLE = ALL,
    AUTHENTICATION = CERTIFICATE dbm_certificate,
ENCRYPTION = REQUIRED ALGORITHM AES
);
GO

ALTER ENDPOINT [Hadr_endpoint] STATE = STARTED;
```

### <a name="create-the-availability-group"></a>Creare il gruppo di disponibilità.

Connettersi all'istanza di SQL Server che ospita la replica primaria usando SQL CMD o SSMS. Per creare il gruppo di disponibilità, eseguire questo comando:

- Sostituire `ag1` con il nome del gruppo di disponibilità desiderato.
- Sostituire i valori di `<VM1>`, `<VM2>` e `<VM3>` con i nomi delle istanze di SQL Server che ospitano le repliche.

```sql
CREATE AVAILABILITY GROUP [ag1]
     WITH (DB_FAILOVER = ON, CLUSTER_TYPE = EXTERNAL)
     FOR REPLICA ON
         N'<VM1>'
          WITH (
             ENDPOINT_URL = N'tcp://<VM1>:5022',
             AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
             FAILOVER_MODE = EXTERNAL,
             SEEDING_MODE = AUTOMATIC
             ),
         N'<VM2>'
          WITH (
             ENDPOINT_URL = N'tcp://<VM2>:5022',
             AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
             FAILOVER_MODE = EXTERNAL,
             SEEDING_MODE = AUTOMATIC
             ),
         N'<VM3>'
         WITH(
            ENDPOINT_URL = N'tcp://<VM3>:5022',
            AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
            FAILOVER_MODE = EXTERNAL,
            SEEDING_MODE = AUTOMATIC
            );
GO

ALTER AVAILABILITY GROUP [ag1] GRANT CREATE ANY DATABASE;
```

### <a name="create-a-sql-server-login-for-pacemaker"></a>Creare un account di accesso di SQL Server per Pacemaker

In tutte le istanze di SQL Server creare un account di accesso SQL per Pacemaker. Il codice Transact-SQL seguente crea un account di accesso.

- Sostituire `<password>` con una password complessa personale.

```sql
USE [master]
GO
CREATE LOGIN [pacemakerLogin] with PASSWORD= N'<password>';
GO
ALTER SERVER ROLE [sysadmin] ADD MEMBER [pacemakerLogin];
```

In tutte le istanze di SQL Server salvare le credenziali usate per l'account di accesso di SQL Server. 

1. Creare il file:

    ```bash
    sudo vi /var/opt/mssql/secrets/passwd
    ```

1. Aggiungere al file le 2 righe seguenti:

    ```bash
    pacemakerLogin
    <password>
    ```

    Per uscire dall'editor **vi**, premere prima il tasto **ESC** e quindi immettere il comando `:wq` per eseguire la scrittura del file e uscire.

1. Impostare il file come leggibile solo dall'utente ROOT:

    ```bash
    sudo chown root:root /var/opt/mssql/secrets/passwd
    sudo chmod 400 /var/opt/mssql/secrets/passwd
    ```

### <a name="join-secondary-replicas-to-the-availability-group"></a>Aggiungere le repliche secondarie al gruppo di disponibilità

1. Per aggiungere le repliche secondarie al gruppo di disponibilità, è necessario aprire la porta 5022 sul firewall per tutti i server. Nella sessione SSH eseguire questo comando:

    ```bash
    sudo firewall-cmd --zone=public --add-port=5022/tcp --permanent
    sudo firewall-cmd --reload
    ```

1. Nelle repliche secondarie eseguire i comandi seguenti per aggiungerle al gruppo di disponibilità:

    ```sql
    ALTER AVAILABILITY GROUP [ag1] JOIN WITH (CLUSTER_TYPE = EXTERNAL);
    GO

    ALTER AVAILABILITY GROUP [ag1] GRANT CREATE ANY DATABASE;
    ```

1. Nella replica primaria e in ogni replica secondaria eseguire lo script Transact-SQL seguente:

    ```sql
    GRANT ALTER, CONTROL, VIEW DEFINITION ON AVAILABILITY GROUP::ag1 TO pacemakerLogin;
    GO
    
    GRANT VIEW SERVER STATE TO pacemakerLogin;
    ```

1. Dopo aver aggiunto le repliche secondarie, sarà possibile visualizzarle in Esplora oggetti di SSMS espandendo il nodo **Disponibilità elevata Always On**:

    ![availability-group-joined.png](media/sql-server-linux-rhel-ha-stonith-tutorial/availability-group-joined.png)

### <a name="add-a-database-to-the-availability-group"></a>Aggiungere un database al gruppo di disponibilità

Si seguiranno le procedure illustrate nell'articolo sulla [configurazione del gruppo di disponibilità per aggiungere un database](/sql/linux/sql-server-linux-availability-group-configure-ha#add-a-database-to-the-availability-group).

In questo passaggio verranno usati i comandi Transact-SQL seguenti. Eseguire questi comandi nella replica primaria:

```sql
CREATE DATABASE [db1]; -- creates a database named db1
GO

ALTER DATABASE [db1] SET RECOVERY FULL; -- set the database in full recovery mode
GO

BACKUP DATABASE [db1] -- backs up the database to disk
   TO DISK = N'/var/opt/mssql/data/db1.bak';
GO

ALTER AVAILABILITY GROUP [ag1] ADD DATABASE [db1]; -- adds the database db1 to the AG
```

### <a name="verify-that-the-database-is-created-on-the-secondary-servers"></a>Verificare che il database sia creato nei server secondari

In ogni replica secondaria di SQL Server eseguire la query seguente per verificare che il database db1 sia stato creato e che si trovi nello stato SINCRONIZZATO:

```
SELECT * FROM sys.databases WHERE name = 'db1';
GO
SELECT DB_NAME(database_id) AS 'database', synchronization_state_desc FROM sys.dm_hadr_database_replica_states;
```

Se l'elenco `synchronization_state_desc` restituisce SINCRONIZZATO per `db1`, significa che le repliche sono sincronizzate. I server secondari mostrano `db1` nella replica primaria.

## <a name="create-availability-group-resources-in-the-pacemaker-cluster"></a>Creare le risorse del gruppo di disponibilità nel cluster Pacemaker

Verranno seguite le istruzioni fornite nella guida [Creare le risorse dei gruppi di disponibilità nel cluster Pacemaker](/sql/linux/sql-server-linux-create-availability-group]#create-the-availability-group-resources-in-the-pacemaker-cluster-external-only).

### <a name="create-the-ag-cluster-resource"></a>Creare la risorsa cluster del gruppo di disponibilità

1. Usare il comando seguente per creare la risorsa `ag_cluster` nel gruppo di disponibilità `ag1`.

    ```bash
    sudo pcs resource create ag_cluster ocf:mssql:ag ag_name=ag1 meta failure-timeout=30s master notify=true
    ```

1. Controllare la risorsa e verificare che sia online prima di procedere con il comando seguente:

    ```bash
    sudo pcs resource
    ```

    Dovrebbe venire visualizzato l'output seguente.

    ```output
    [<username>@VM1 ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
    Masters: [ <VM1> ]
    Slaves: [ <VM2> <VM3> ]
    virtualip      (ocf::heartbeat:IPaddr2):       Started <VM1>
    ```

### <a name="create-a-virtual-ip-resource"></a>Creare una risorsa indirizzo IP virtuale

1. Usare un indirizzo IP statico disponibile della rete per creare una risorsa indirizzo IP virtuale. È possibile trovarne uno usando lo strumento di comando `nmap`.

    ```bash
    nmap -sP <IPRange>
    # For example: nmap -sP 10.0.0.*
    # The above will scan for all IP addresses that are already occupied in the 10.0.0.x space.
    ```

1. Impostare la proprietà **stonith-enabled** su false

    ```bash
    sudo pcs property set stonith-enabled=false
    ```

1. Per creare la risorsa indirizzo IP virtuale, usare il comando seguente:

    - Sostituire il valore per `<availableIP>` con un indirizzo IP inutilizzato.

    ```bash
    sudo pcs resource create virtualip ocf:heartbeat:IPaddr2 ip=<availableIP>
    ```

### <a name="add-constraints"></a>Aggiungere i vincoli

1. Per fare in modo che la risorsa indirizzo IP e la risorsa gruppo di disponibilità siano in esecuzione nello stesso nodo, è necessario configurare un vincolo di condivisione del percorso. Eseguire il comando seguente:

    ```bash
    sudo pcs constraint colocation add virtualip ag_cluster-master INFINITY with-rsc-role=Master
    ```

1. Creare un vincolo di ordinamento per fare in modo che la risorsa del gruppo di disponibilità sia attiva e in esecuzione prima dell'indirizzo IP. Mentre il vincolo di condivisione del percorso implica un vincolo di ordinamento, questo lo applica.

    ```bash
    sudo pcs constraint order promote ag_cluster-master then start virtualip
    ```

1. Per verificare i vincoli, eseguire questo comando:

    ```bash
    sudo pcs constraint list --full
    ```

    Dovrebbe venire visualizzato l'output seguente.

    ```
    Location Constraints:
    Ordering Constraints:
          promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
    Colocation Constraints:
          virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```

### <a name="re-enable-stonith"></a>Riabilitare STONITH

A questo punto, è possibile procedere al test. Per riabilitare STONITH nel cluster eseguire questo comando nel nodo 1:

```bash
sudo pcs property set stonith-enabled=true
```

### <a name="check-cluster-status"></a>Controllare lo stato del cluster

È possibile controllare lo stato delle risorse cluster con il comando seguente:

```output
[<username>@VM1 ~]$ sudo pcs status
Cluster name: az-hacluster
Stack: corosync
Current DC: <VM3> (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
Last updated: Sat Dec  7 00:18:38 2019
Last change: Sat Dec  7 00:18:02 2019 by root via cibadmin on VM1

3 nodes configured
5 resources configured

Online: [ <VM1> <VM2> <VM3> ]

Full list of resources:

 Master/Slave Set: ag_cluster-master [ag_cluster]
     Masters: [ <VM2> ]
     Slaves: [ <VM1> <VM3> ]
 virtualip      (ocf::heartbeat:IPaddr2):       Started <VM2>
 rsc_st_azure   (stonith:fence_azure_arm):      Started <VM1>

Daemon Status:
  corosync: active/enabled
  pacemaker: active/enabled
  pcsd: active/enabled
```

## <a name="test-failover"></a>Failover di test

Per assicurarsi che la configurazione abbia avuto esito positivo fino a questo momento, si testerà un failover. Per altre informazioni, vedere [Failover di un gruppo di disponibilità Always On in Linux](/sql/linux/sql-server-linux-availability-group-failover-ha).

1. Eseguire il comando seguente per procedere al failover manuale della replica primaria in `<VM2>`. Sostituire `<VM2>` con il valore del nome del server.

    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master
    ```

1. Se si controllano di nuovo i vincoli, si noterà che è stato aggiunto un altro vincolo a seguito del failover manuale:

    ```output
    [<username>@VM1 ~]$ sudo pcs constraint list --full
    Location Constraints:
          Resource: ag_cluster-master
            Enabled on: VM2 (score:INFINITY) (role: Master) (id:cli-prefer-ag_cluster-master)
    Ordering Constraints:
            promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
    Colocation Constraints:
            virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```

1. Rimuovere il vincolo con l'ID `cli-prefer-ag_cluster-master` usando il comando seguente:

    ```bash
    sudo pcs constraint remove cli-prefer-ag_cluster-master
    ```

1. Controllare le risorse cluster usando il comando `sudo pcs resource`. Si noterà che l'istanza primaria è ora `<VM2>`.

    ```output
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
         ag_cluster (ocf::mssql:ag):        FAILED <VM1> (Monitoring)
         Masters: [ <VM2> ]
         Slaves: [ <VM3> ]
    virtualip      (ocf::heartbeat:IPaddr2):       Started <VM2>
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
         Masters: [ <VM2> ]
         Slaves: [ <VM1> <VM3> ]
    virtualip      (ocf::heartbeat:IPaddr2):       Started <VM2>
     
    ```

## <a name="test-fencing"></a>Testare l'isolamento

Per testare STONITH, eseguire il comando seguente. Provare a eseguire il comando seguente da `<VM1>` per `<VM3>`.

```bash
sudo pcs stonith fence <VM3> --debug
```

> [!NOTE]
> Per impostazione predefinita, l'azione di isolamento porta il nodo offline e quindi di nuovo online. Se si vuole solo portare offline il nodo, usare l'opzione `--off` nel comando.

Verrà visualizzato l'output seguente:

```output
[<username>@<VM1> ~]$ sudo pcs stonith fence <VM3> --debug
Running: stonith_admin -B <VM3>
Return Value: 0
--Debug Output Start--
--Debug Output End--
 
Node: <VM3> fenced
```
Per altre informazioni sul test di un dispositivo di isolamento, vedere l'articolo di [Red Hat](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/s1-stonithtest-haar) seguente.

## <a name="next-steps"></a>Passaggi successivi

Per usare un listener del gruppo di disponibilità per i server SQL creati in Azure, sarà prima necessario creare e configurare un servizio di bilanciamento del carico.

> [!div class="nextstepaction"]
> [Creare e configurare il servizio di bilanciamento del carico nel portale di Azure](../../../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md#create-and-configure-the-load-balancer-in-the-azure-portal)