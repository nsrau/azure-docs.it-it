---
title: Risolvere i problemi di distribuzione in Kubernetes in Azure Stack | Microsoft Docs
description: Informazioni su come risolvere i problemi di distribuzione in Kubernetes in Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: dc6994cd682997dd00e76b57b4996ebad1f73fbb
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2019
ms.locfileid: "54352394"
---
# <a name="troubleshoot-your-deployment-to-kubernetes-to-azure-stack"></a>Risolvere i problemi di distribuzione in Kubernetes in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

> [!Note]  
> Kubernetes in Azure Stack è disponibile in anteprima.

L'articolo seguente esamina la risoluzione dei problemi del cluster Kubernetes. È possibile esaminare l'avviso di distribuzione e rivedere lo stato della distribuzione per gli elementi necessari per la distribuzione. Potrebbe essere necessario raccogliere i log di distribuzione da Azure Stack o le macchine virtuali Linux che ospitano Kubernetes. È anche necessario rivolgersi all'amministratore di Azure Stack per recuperare i log da un endpoint di amministrazione.

## <a name="overview-of-deployment"></a>Panoramica della distribuzione

Prima di iniziare la risoluzione dei problemi del cluster, si potrebbe voler esaminare il processo di distribuzione di cluster Kubernetes di Azure Stack. La distribuzione Usa un modello di soluzione di Azure Resource Manager per creare le macchine virtuali e installare il motore ACS per il cluster.

### <a name="deployment-workflow"></a>Flusso di lavoro per la distribuzione

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
    -  Scarica e si esegue l'estensione script cliente dal marketplace. Lo script fa **dello Script personalizzato per Linux 2.0**.
    -  Eseguire lo script personalizzato di DVM. Lo script esegue le attività seguenti:
        1. Ottiene l'endpoint di raccolta dall'endpoint dei metadati di Azure Resource Manager.
        2. Ottiene l'ID di risorsa di active directory dall'endpoint dei metadati di Azure Resource Manager.
        3. Carica il modello di API per il motore ACS.
        4. Consente di distribuire il modulo di gestione ACS per il cluster Kubernetes e Salva il profilo di cloud di Azure Stack da `/etc/kubernetes/azurestackcloud.json`.
3. Creare le VM master.

4. Scaricare ed eseguire le estensioni degli script dei clienti.

5. Eseguire lo script master.

    Lo script esegue le attività seguenti:
    - Installa etcd, Docker e Kubernetes risorse, ad esempio kubelet. etcd è un archivio di valori chiave distribuita che consente di archiviare i dati in un cluster di computer. Docker supporta la virtualizzazione: a livello di sistema operativo pressoché essenziale noti come contenitori. Kubelet è l'agente del nodo che viene eseguito in ogni nodo di Kubernetes.
    - Configura il servizio etcd.
    - Configura il servizio di kubelet.
    - Avvia kubelet. Questa attività prevede i passaggi seguenti:
        1. Avvia il servizio API.
        2. Avvia il servizio controller.
        3. Avvia il servizio Utilità di pianificazione.
6. Creare l'agente di macchine virtuali.

7. Scarica e si esegue l'estensione script cliente.

7. Eseguire lo script dell'agente. Lo script personalizzato dell'agente effettua le seguenti attività:
    - Installa etcd
    - Configura il servizio di kubelet
    - Viene aggiunto al cluster Kubernetes

## <a name="steps-for-troubleshooting"></a>Passaggi per la risoluzione dei problemi

È possibile raccogliere i log nelle macchine virtuali che supportano il cluster Kubernetes. È anche possibile esaminare il log di distribuzione. Si potrebbe essere necessario rivolgersi all'amministratore di Azure Stack per verificare la versione di Azure Stack che è necessario usare e per ottenere i log da Azure Stack che sono correlati alla distribuzione.

1. Rivedere le [lo stato di distribuzione](#review-deployment-status) e [recuperare i registri](#get-logs-from-a-vm) dal nodo master nel cluster Kubernetes.
2. Assicurarsi che si usa la versione più recente di Azure Stack. Se non si conosce la versione in uso, contattare l'amministratore di Azure Stack. Il tempo di marketplace di cluster Kubernetes 0.3.0 richiede la versione di Azure Stack 1808 o supera.
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
    | type | Il provider di risorse e il tipo di risorsa. |
    | Status | Lo stato dell'elemento. |
    | TimeStamp | Il timestamp UTC del tempo. |
    | Dettagli dell'operazione | I dettagli dell'operazione, ad esempio il provider di risorse che è stato coinvolto nell'operazione, l'endpoint di risorse e il nome della risorsa. |

    Ogni elemento ha un'icona di stato di colore verde oppure rosso.

## <a name="get-logs-from-a-vm"></a>Ottenere i log da una macchina virtuale

Per generare i log, è necessario connettersi al server master di VM per il cluster, aprire un prompt dei comandi bash e quindi eseguire uno script. Lo schema della macchina virtuale sono reperibili nel gruppo di risorse cluster e viene denominata `k8s-master-<sequence-of-numbers>`. 

### <a name="prerequisites"></a>Prerequisiti

È necessario un bash nel computer che si usa per gestire Azure Stack dei messaggi di richiesta. Usare bash per eseguire gli script accedere ai log. In un computer Windows, è possibile usare il prompt di bash che viene installato con Git. Per ottenere la versione più recente di git, vedere [download Git](https://git-scm.com/downloads).

### <a name="get-logs"></a>Ottenere i log

Per ottenere i log, procedere come segue:

1. Aprire un prompt di bash. Se si usa Git in un computer Windows, è possibile aprire un prompt di bash dal seguente percorso: `c:\programfiles\git\bin\bash.exe`.
2. Eseguire i comandi bash seguenti:

    ```Bash  
    mkdir -p $HOME/kuberneteslogs
    cd $HOME/kuberneteslogs
    curl -O https://raw.githubusercontent.com/msazurestackworkloads/azurestack-gallery/master/diagnosis/getkuberneteslogs.sh
    sudo chmod 744 getkuberneteslogs.sh
    ```

    > [!Note]  
    > In Windows, non devi eseguire `sudo`. È possibile usare invece solo `chmod 744 getkuberneteslogs.sh`.

3. Nella stessa sessione, eseguire il comando seguente con i parametri aggiornati per adattarli all'ambiente:

    ```Bash  
    ./getkuberneteslogs.sh --identity-file id_rsa --user azureuser --vmdhost 192.168.102.37
    ```

4. Esaminare i parametri e impostare i valori nel proprio ambiente.
    | Parametro           | DESCRIZIONE                                                                                                      | Esempio                                                                       |
    |---------------------|------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------|
    | -i, --identity-file | File chiave privata RSA per connettere la VM master di Kubernetes. La chiave deve iniziare con `-----BEGIN RSA PRIVATE KEY-----` | C:\data\privatekey.pem                                                        |
    | -h, --host          | L'indirizzo IP pubblico o il nome di dominio completo (FQDN) del master del cluster Kubernetes della macchina virtuale. Il nome VM viene avviata con `k8s-master-`.                       | IP: 192.168.102.37<br><br>FQDN: k8s-12345.local.cloudapp.azurestack.external      |
    | -u, --utente          | Il nome utente del master del cluster Kubernetes della macchina virtuale. Questo nome viene impostato quando si configura l'elemento del marketplace.                                                                    | azureuser                                                                     |
    | -d, --vmdhost       | L'indirizzo IP pubblico o il FQDN di DVM. Il nome VM viene avviata con `vmd-`.                                                       | IP: 192.168.102.38<br><br>DNS: vmd-dnsk8-frog.local.cloudapp.azurestack.external |

   Quando si aggiungono i valori dei parametri, che potrebbe essere simile al codice seguente:

    ```Bash  
    ./getkuberneteslogs.sh --identity-file "C:\secretsecret.pem" --user azureuser --vmdhost 192.168.102.37
     ```

    I registri vengono creati al completamento dell'esecuzione.

    ![Log generati](media/azure-stack-solution-template-kubernetes-trouble/azure-stack-generated-logs.png)


4. Recuperare i log nelle cartelle create dal comando. Il comando Crea nuove cartelle e un timestamp li.
    - KubernetesLogs*YYYY-MM-DD-XX-XX-XX-XXX*
        - Dvmlogs
        - Acsengine-kubernetes-dvm.log

## <a name="next-steps"></a>Passaggi successivi

[Distribuzione di Kubernetes in Azure Stack](azure-stack-solution-template-kubernetes-deploy.md)

[Aggiungere un cluster Kubernetes nel Marketplace (per l'operatore di Azure Stack)](../azure-stack-solution-template-kubernetes-cluster-add.md)

[Kubernetes in Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
