---
title: Risolvere i problemi di distribuzione di Kubernetes in Azure Stack | Microsoft Docs
description: Informazioni su come risolvere i problemi di distribuzione di Kubernetes in Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.author: mabrigg
ms.date: 04/02/2019
ms.reviewer: waltero
ms.lastreviewed: 03/20/2019
ms.openlocfilehash: 2a9eccfa109292b7d142092f69f4a664b0ff8f20
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58878129"
---
# <a name="troubleshoot-kubernetes-deployment-to-azure-stack"></a>Risolvere i problemi di distribuzione di Kubernetes in Azure Stack

*Si applica a Azure Stack Development Kit e i sistemi integrati di Azure Stack*

> [!Note]  
> Kubernetes in Azure Stack è disponibile in anteprima. Scenario disconnesso di Azure Stack non è attualmente supportato per l'anteprima.

L'articolo seguente esamina la risoluzione dei problemi del cluster Kubernetes. È possibile esaminare l'avviso di distribuzione e rivedere lo stato della distribuzione per gli elementi necessari per la distribuzione. Potrebbe essere necessario raccogliere i log di distribuzione da Azure Stack o le macchine virtuali Linux che ospitano Kubernetes. È anche necessario rivolgersi all'amministratore di Azure Stack per recuperare i log da un endpoint di amministrazione.

## <a name="overview-of-kubernetes-deployment"></a>Panoramica della distribuzione di Kubernetes

Prima di iniziare la risoluzione dei problemi del cluster, si potrebbe voler esaminare il processo di distribuzione di cluster Kubernetes di Azure Stack. La distribuzione Usa un modello di soluzione di Azure Resource Manager per creare le macchine virtuali e installare il motore ACS per il cluster.

### <a name="kubernetes-deployment-workflow"></a>Flusso di lavoro distribuzione Kubernetes

Il diagramma seguente illustra il processo generale per distribuire il cluster.

![Distribuire il processo di Kubernetes](media/azure-stack-solution-template-kubernetes-trouble/002-Kubernetes-Deploy-Flow.png)

### <a name="deployment-steps"></a>Passaggi di distribuzione

1. Raccolta di parametri di input dall'elemento di marketplace.

    Immettere i valori necessari per configurare il cluster Kubernetes, tra cui:
    -  **User name** (Nome utente): Il nome utente per le macchine virtuali Linux che fanno parte di un cluster Kubernetes e DVM.
    -  **Chiave pubblica SSH**: La chiave che viene usata per l'autorizzazione di tutti i computer Linux che sono stati creati come parte del cluster Kubernetes e DVM.
    -  **Entità servizio**: L'ID utilizzato dal provider di cloud di Azure in Kubernetes. L'ID client è stato identificato come l'ID dell'applicazione durante la creazione dell'entità servizio. 
    -  **Segreto client**: Chiave creato al momento della creazione dell'entità servizio.

2. Creare la distribuzione della macchina virtuale e l'estensione script personalizzato.
    -  Creare la distribuzione di VM Linux usando l'immagine Linux in marketplace **Ubuntu Server 16.04-LTS**.
    -  Scaricare ed eseguire l'estensione script personalizzato dal marketplace. Lo script fa **dello Script personalizzato per Linux 2.0**.
    -  Eseguire lo script personalizzato di DVM. Lo script esegue le attività seguenti:
        1. Ottiene l'endpoint di raccolta dall'endpoint dei metadati di Azure Resource Manager.
        2. Ottiene l'ID di risorsa di active directory dall'endpoint dei metadati di Azure Resource Manager.
        3. Carica il modello di API per il motore ACS.
        4. Consente di distribuire il modulo di gestione ACS per il cluster Kubernetes e Salva il profilo di cloud di Azure Stack da `/etc/kubernetes/azurestackcloud.json`.
3. Creare le VM master.

4. Scaricare ed eseguire le estensioni degli script personalizzati.

5. Eseguire lo script master.

    Lo script esegue le attività seguenti:
    - Installa etcd, Docker e Kubernetes risorse, ad esempio kubelet. etcd è un archivio di valori chiave distribuita che consente di archiviare i dati in un cluster di computer. Docker supporta la virtualizzazione: a livello di sistema operativo pressoché essenziale noti come contenitori. Kubelet è l'agente del nodo che viene eseguito in ogni nodo di Kubernetes.
    - Configura il **etcd** servizio.
    - Configura il **kubelet** servizio.
    - Avvia kubelet. Questa attività prevede i passaggi seguenti:
        1. Avvia il servizio API.
        2. Avvia il servizio controller.
        3. Avvia il servizio Utilità di pianificazione.
6. Creare l'agente di macchine virtuali.

7. Scaricare ed eseguire l'estensione script personalizzata.

7. Eseguire lo script dell'agente. Lo script personalizzato dell'agente effettua le seguenti attività:
    - Consente di installare **etcd**.
    - Configura il **kubelet** servizio.
    - Crea un join al cluster Kubernetes.

## <a name="steps-for-troubleshooting"></a>Passaggi per la risoluzione dei problemi

È possibile raccogliere i log nelle macchine virtuali che supportano il cluster Kubernetes. È anche possibile esaminare il log di distribuzione. Si potrebbe essere necessario rivolgersi all'amministratore di Azure Stack per verificare la versione di Azure Stack che è necessario usare e per ottenere i log da Azure Stack che sono correlati alla distribuzione.

1. Rivedere le [lo stato di distribuzione](#review-deployment-status) e recuperare i registri dal nodo master nel cluster Kubernetes.
2. Assicurarsi che si usa la versione più recente di Azure Stack. Se non si conosce la versione in uso, contattare l'amministratore di Azure Stack.
3.  Esaminare i file di creazione della macchina virtuale. Si potrebbero avere avuto problemi seguenti:  
    - La chiave pubblica potrebbe non essere valida. Esaminare la chiave creata.  
    - Creazione della macchina virtuale è possibile che hanno generato un errore interno o ha generato un errore di creazione. Numerosi fattori possono causare errori, quali i limiti di capacità per la sottoscrizione di Azure Stack.
    - Assicurarsi che il nome di dominio completo (FQDN) per la macchina virtuale inizia con un prefisso duplicato.
4.  Se la macchina virtuale viene **OK**, quindi valutare di DVM. Se il DVM dispone di un messaggio di errore:

    - La chiave pubblica potrebbe non essere valida. Esaminare la chiave creata.  
    - È necessario contattare l'amministratore di Azure Stack per recuperare i log per Azure Stack tramite gli endpoint con privilegi. Per altre informazioni, vedere [strumenti di diagnostica di Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostics).
5. Se hai una domanda sulla distribuzione, è possibile pubblicarlo o vedere se un utente ha già risposto alla domanda nel [forum di Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). 

## <a name="review-deployment-status"></a>Esaminare lo stato di distribuzione

Quando si distribuisce il cluster Kubernetes, è possibile esaminare lo stato della distribuzione per verificare la presenza di eventuali problemi.

1. Aprire il [portale di Azure Stack](https://portal.local.azurestack.external).
2. Selezionare **gruppi di risorse**e quindi selezionare il nome del gruppo di risorse usato durante la distribuzione del cluster Kubernetes.
3. Selezionare **distribuzioni**, quindi selezionare la **nome distribuzione**.

    ![risoluzione dei problemi](media/azure-stack-solution-template-kubernetes-trouble/azure-stack-kub-trouble-report.png)

4.  Consultare la finestra Risoluzione dei problemi. Ogni risorsa distribuita fornisce le informazioni seguenti:
    
    | Proprietà | DESCRIZIONE |
    | ----     | ----        |
    | Risorsa | Nome della risorsa. |
    | Type | Il provider di risorse e il tipo di risorsa. |
    | Stato | Lo stato dell'elemento. |
    | TimeStamp | Il timestamp UTC del tempo. |
    | Dettagli dell'operazione | I dettagli dell'operazione, ad esempio il provider di risorse che è stato coinvolto nell'operazione, l'endpoint di risorse e il nome della risorsa. |

    Ogni elemento ha un'icona di stato di colore verde oppure rosso.

## <a name="review-deployment-logs"></a>Esaminare i registri di distribuzione

Se il portale di Azure Stack non fornisce informazioni sufficienti per consentire di risolvere i problemi o risolvere un errore di distribuzione, il passaggio successivo è approfondire i log del cluster. Per recuperare manualmente i registri di distribuzione, è necessario in genere connettersi a una delle macchine virtuali master del cluster. Un approccio alternativo più semplice, è possibile scaricare ed eseguire il comando seguente [script Bash](https://aka.ms/AzsK8sLogCollectorScript) forniti dal team di Azure Stack. Questo script si connette a macchine virtuali del cluster e di DVM raccoglie sistema più importanti e log del cluster e li scarica nuovamente alla workstation.

### <a name="prerequisites"></a>Prerequisiti

È necessario un prompt di Bash nel computer usato per gestire Azure Stack. In un computer Windows, è possibile ottenere un Bash prompt installando [Git per Windows](https://git-scm.com/downloads). Una volta installato, cercare _Git Bash_ nel menu start.

### <a name="retrieving-the-logs"></a>Recupero di log

Seguire questi passaggi per la raccolta e scaricare i log del cluster:

1. Aprire un prompt di Bash. Da un computer Windows, aprire _Git Bash_ oppure eseguire: `C:\Program Files\Git\git-bash.exe`.

2. Scaricare lo script di log collector eseguendo i comandi seguenti nel prompt di Bash:

    ```Bash  
    mkdir -p $HOME/kuberneteslogs
    cd $HOME/kuberneteslogs
    curl -O https://raw.githubusercontent.com/msazurestackworkloads/azurestack-gallery/master/diagnosis/getkuberneteslogs.sh
    chmod 744 getkuberneteslogs.sh
    ```

3. Cercare le informazioni necessarie per lo script ed eseguirlo:

    | Parametro           | DESCRIZIONE                                                                                                      | Esempio                                                                       |
    |---------------------|------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------|
    | -d, --vmd-host      | L'indirizzo IP pubblico o il nome di dominio completo (FQDN) di DVM. Nome della macchina virtuale viene avviata con `vmd-`. | IP: 192.168.102.38<br>DNS: vmd-myk8s.local.cloudapp.azurestack.external |
    | -h, --help  | Uso del comando di stampa. | |
    | -i, --identity-file | Il file di chiave privato RSA passato per l'elemento del marketplace quando si crea il cluster Kubernetes. Necessario per in modalità remota a nodi Kubernetes. | C:\data\id_rsa.PEM (Putty)<br>~/.SSH/id_rsa (SSH)
    | -m, --master-host   | L'indirizzo IP pubblico o il nome di dominio completo (FQDN) di un nodo master di Kubernetes. Nome della macchina virtuale viene avviata con `k8s-master-`. | IP: 192.168.102.37<br>FQDN: k8s-12345.local.cloudapp.azurestack.external      |
    | -u, --utente          | Il nome utente passato per l'elemento del marketplace quando si crea il cluster Kubernetes. Dovevano in modalità remota a nodi Kubernetes | azureuser (valore predefinito) |


   Quando si aggiungono i valori dei parametri, il comando potrebbe essere simile al seguente:

    ```Bash  
    ./getkuberneteslogs.sh --identity-file "C:\id_rsa.pem" --user azureuser --vmd-host 192.168.102.37
     ```

4. Dopo alcuni minuti, lo script genererà i log raccolti in una directory denominata `KubernetesLogs_{{time-stamp}}`. Qui troverai una directory per ogni macchina virtuale che appartiene al cluster.

    Lo script dell'agente di raccolta log anche cercare errori nei file di log e includere passaggi di risoluzione dei problemi se si verifica per individuare un problema noto. Assicurarsi che si esegue la versione più recente dello script per aumentare la probabilità di trovare i problemi noti.

> [!Note]  
> Consulta questo GitHub [repository](https://github.com/msazurestackworkloads/azurestack-gallery/tree/master/diagnosis) per altre informazioni dettagliate sullo script dell'agente di raccolta log.

## <a name="next-steps"></a>Passaggi successivi

[Distribuzione di Kubernetes in Azure Stack](azure-stack-solution-template-kubernetes-deploy.md)

[Aggiungere un cluster Kubernetes nel Marketplace (per l'operatore di Azure Stack)](../azure-stack-solution-template-kubernetes-cluster-add.md)

[Kubernetes in Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
