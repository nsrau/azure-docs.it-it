---
title: Risolvere i problemi di distribuzione in Kubernetes (K8) ad Azure Stack | Microsoft Docs
description: Informazioni su come risolvere i problemi di distribuzione in Kubernetes (K8) ad Azure Stack.
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
ms.date: 10/11/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: fbb51d8dc3b1ea4c6b34120e8fe35474ae949cf2
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2018
ms.locfileid: "49116913"
---
# <a name="troubleshoot-your-deployment-to-kubernetes-to-azure-stack"></a>Risolvere i problemi di distribuzione in Kubernetes in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

> [!Note]  
> Kubernetes in Azure Stack è disponibile in anteprima.

L'articolo seguente esamina la risoluzione dei problemi del cluster Kubernetes. È possibile esaminare l'avviso di distribuzione e rivedere lo stato della distribuzione per gli elementi necessari per la distribuzione. Potrebbe essere necessario raccogliere i log di distribuzione da Azure Stack o le macchine virtuali Linux che ospitano Kubernetes. Inoltre, si potrebbe essere necessario contattare l'amministratore di Azure Stack per recuperare i log da un endpoint di amministrazione.

## <a name="overview-of-deployment"></a>Panoramica della distribuzione

Prima di entrare nei passaggi per risolvere i problemi del cluster, è possibile esaminare il processo di distribuzione di cluster Kubernetes di Azure Stack. La distribuzione Usa un modello di soluzione di Azure Resource Manager per creare le macchine virtuali e installa il motore ACS per il cluster.

### <a name="deployment-workflow"></a>Flusso di lavoro di distribuzione

Il diagramma seguente illustra il processo generale per distribuire il cluster.

![Distribuire il processo di Kubernetes](media/azure-stack-solution-template-kubernetes-trouble/002-Kubernetes-Deploy-Flow.png)

### <a name="deployment-steps"></a>Passaggi di distribuzione

1. Raccoglie i parametri dall'elemento di marketplace di input.

    Immettere i valori che necessari per configurare il cluster Kubernetes tra cui:
    -  **Nome utente** nome utente per le macchine virtuali di Linux che fanno parte del cluster Kubernetes e DVM.
    -  **Chiave pubblica SSH** la chiave usata per l'autorizzazione a tutti i computer Linux creato come parte del cluster Kubernetes e DVM
    -  **Entità servizio** ID utilizzato dal provider di cloud di Azure in Kubernetes. L'ID Client è stato identificato come l'ID dell'applicazione quando si crea l'entità servizio. 
    -  **Segreto client** la chiave creata durante la creazione dell'entità servizio.

2. Crea macchina virtuale della distribuzione e l'estensione script personalizzato.
    -  Crea la distribuzione di VM Linux usando l'immagine di Linux, marketplace **Ubuntu Server 16.04-LTS**.
    -  Scaricare ed eseguire l'estensione script cliente dal marketplace. Lo script è il **dello Script personalizzato per Linux 2.0**.
    -  Esegue lo script personalizzato di DVM. Lo script:
        1. Ottiene l'endpoint di raccolta dall'endpoint dei metadati di Azure Resource Manager.
        2. Ottiene l'ID di risorsa di active directory dall'endpoint dei metadati di Azure Resource Manager.
        3. Carica il modello di API per il motore ACS.
        4. Consente di distribuire il modulo di gestione ACS per il cluster Kubernetes e Salva il profilo di cloud di Azure Stack da `/etc/kubernetes/azurestackcloud.json`.
3. Crea macchine virtuali master.

    Scarica ed esegue l'estensione script cliente.

4. Esegue lo script master.

    Lo script:
    - Installa etcd, Docker e Kubernetes risorse, ad esempio kubelet. etcd è un archivio di valori chiave distribuita che consente di archiviare i dati in un cluster di computer. Docker supporta pressoché essenziale del sistema operativo a livello di virtualizzazione: noti come contenitori. Kubelet è l'agente del nodo che viene eseguito in ogni nodo di Kubernetes.
    - Configura il servizio etcd.
    - Configura servizio Kubelet.
    - Avvia kubelet. Ciò comporta quanto segue:
        1. Avvia il servizio API.
        2. Avvia il servizio Controller.
        3. Avvia servizio Utilità di pianificazione.
5. Crea agente di macchine virtuali.

    Scarica ed esegue l'estensione script cliente.

6. Viene eseguito lo script dell'agente. Lo script personalizzato dell'agente:
    - Installare etcd.
    - Configurare il servizio di Kubelet.
    - Crea un join al cluster Kubernetes.

## <a name="steps-for-troubleshooting"></a>Passaggi per la risoluzione dei problemi

È possibile raccogliere i log nelle macchine virtuali che supportano il cluster Kubernetes. È anche possibile esaminare il log di distribuzione. È necessario anche contattare l'amministratore di Azure Stack per verificare la versione di Azure Stack in uso e per ottenere i log da Azure Stack correlati alla distribuzione.

1. Esaminare i [lo stato di distribuzione](#review-deployment-status) e il [recuperare i registri](#get-logs-from-a-vm) dal nodo master nel cluster Kubernetes.
2. È necessario usare la versione più recente di Azure Stack. Se si è certi della versione di Azure Stack, contattare l'amministratore di Azure Stack. Il tempo di marketplace di un Kubernetes Cluster 0.3.0 richiede la versione di Azure Stack 1808 o supera.
3.  Esaminare i file di creazione della macchina virtuale. Si potrebbero essersi verificato quanto segue:  
    - La chiave pubblica potrebbe non essere valida. Esaminare la chiave che è stato creato.  
    - Creazione della macchina virtuale può avere generato un errore interno o ha generato un errore di creazione. Gli errori potrebbero essere causati da una serie di fattori quali i limiti di capacità per la sottoscrizione di Azure Stack.
    - Il nome di dominio completo (FDQN) per la macchina virtuale inizia con un prefisso duplicato?
4.  Se la macchina virtuale viene **OK**, quindi, valutare di DVM. Se il DVM dispone di un messaggio di errore:

    - La chiave pubblica potrebbe non essere valida. Esaminare la chiave che è stato creato.  
     - È necessario contattare l'amministratore di Azure Stack per recuperare i log di Azure Stack tramite i punti finali con privilegi. Per altre informazioni, vedere [strumenti di diagnostica di Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostics).
5. Se hai domande sulla distribuzione, è possibile pubblicare una domanda o vedere se un utente ha già risposto alla domanda nel [Forum di Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). 

## <a name="review-deployment-status"></a>Esaminare lo stato di distribuzione

Quando si distribuisce il cluster Kubernetes per esaminare gli eventuali problemi, è possibile esaminare lo stato della distribuzione.

1. Aprire il [portale di Azure Stack](https://portal.local.azurestack.external).
2. Selezionare **gruppi di risorse**, e quindi selezionare il nome del gruppo di risorse usato quando la distribuzione del cluster Kubernetes.
3. Selezionare **distribuzioni** e quindi la **nome distribuzione**.

    ![risoluzione dei problemi](media/azure-stack-solution-template-kubernetes-trouble/azure-stack-kub-trouble-report.png)

4.  Consultare la finestra Risoluzione dei problemi. Ogni risorsa distribuita fornisce le informazioni seguenti.
    
    | Proprietà | DESCRIZIONE |
    | ----     | ----        |
    | Risorsa | Nome della risorsa. |
    | type | Il provider di risorse e il tipo di risorsa. |
    | Status | Lo stato dell'elemento. |
    | Timestamp | Il timestamp UTC del tempo. |
    | Dettagli dell'operazione | I dettagli dell'operazione, ad esempio il provider di risorse necessari per l'operazione, il punto finale di risorse e il nome della risorsa. |

    Ogni elemento avrà un'icona di stato di colore rosse o verde.

## <a name="get-logs-from-a-vm"></a>Ottenere i log da una macchina virtuale

Si verrà necessario per la connessione al server master di VM per il cluster, aprire un prompt dei comandi bash ed eseguire uno script per generare i log. Il master è reperibile nel gruppo di risorse cluster e viene denominato `k8s-master-<sequence-of-numbers>`. 

### <a name="prerequisites"></a>Prerequisiti

È necessario un bash richiesto nel computer all'utilizzo per la gestione di Azure Stack. Usare bash per eseguire gli script accedere ai log. In un computer Windows, è possibile usare il prompt di bash installato con Git. Per ottenere la versione più recente di git, vedere [download git](https://git-scm.com/downloads).

### <a name="get-logs"></a>Ottenere i log

1. Aprire un prompt di bash. Se si usa git in un computer Windows, è possibile aprire un prompt di bash dal seguente percorso: `c:\programfiles\git\bin\bash.exe`.
2. Eseguire i comandi bash seguenti:

    ```Bash  
    mkdir -p $HOME/kuberneteslogs
    cd $HOME/kuberneteslogs
    curl -O https://raw.githubusercontent.com/msazurestackworkloads/azurestack-gallery/master/diagnosis/getkuberneteslogs.sh
    sudo chmod 744 getkuberneteslogs.sh
    ```

    > [!Note]  
    > In Windows, non è necessario eseguire `sudo` e possa usare semplicemente `chmod 744 getkuberneteslogs.sh`.

3. Nella stessa sessione, eseguire il comando seguente con i parametri aggiornati per adattarli all'ambiente.

    ```Bash  
    ./getkuberneteslogs.sh --identity-file id_rsa --user azureuser --vmdhost 192.168.102.37
    ```

    Esaminare i parametri e impostare i valori nel proprio ambiente.
    | Parametro           | DESCRIZIONE                                                                                                      | Esempio                                                                       |
    |---------------------|------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------|
    | -i,-identity-file | Il file di chiave privata RSA per connettere il VM master di kubernetes. La chiave deve iniziare con `-----BEGIN RSA PRIVATE KEY-----` | C:\data\privatekey.PEM                                                        |
    | -h, --host          | L'indirizzo ip pubblico o il nome di dominio completo (FQDN) del master del cluster Kubernetes della macchina virtuale. Il nome VM viene avviata con `k8s-master-`.                       | IP: 192.168.102.37<br><br>FQDN: k8s-12345.local.cloudapp.azurestack.external      |
    | -u, --utente          | Il nome utente del master del cluster Kubernetes della macchina virtuale. Questo nome è impostato quando si configura l'elemento del marketplace.                                                                    | azureuser                                                                     |
    | -d, - vmdhost       | L'indirizzo ip pubblico o il FQDN di DVM. Il nome VM viene avviata con `vmd-`.                                                       | IP: 192.168.102.38<br><br>DNS: vmd-dnsk8-frog.local.cloudapp.azurestack.external |

   Quando si aggiungono i valori dei parametri, che potrebbe simile a:

    ```Bash  
    ./getkuberneteslogs.sh --identity-file "C:\secretsecret.pem" --user azureuser --vmdhost 192.168.102.37
     ```

    I registri vengono creati al completamento dell'esecuzione.

    ![Log generati](media/azure-stack-solution-template-kubernetes-trouble/azure-stack-generated-logs.png)


4. Recuperare i log nelle cartelle create dal comando. Il comando creerà una nuova cartella e il timestamp.
    - KubernetesLogs*YYYY-MM-DD-XX-XX-XX-XXX*
        - Dvmlogs
        - Acsengine-kubernetes-dvm.log

## <a name="next-steps"></a>Passaggi successivi

[Distribuzione di Kubernetes in Azure Stack](azure-stack-solution-template-kubernetes-deploy.md).

[Aggiungere un Kubernetes nel Marketplace (per l'operatore di Azure Stack)](..\azure-stack-solution-template-kubernetes-cluster-add.md)

[Kubernetes in Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
