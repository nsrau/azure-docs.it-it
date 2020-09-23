---
title: Creare e gestire un cluster Kubernetes nel dispositivo GPU Pro Azure Stack Edge | Microsoft Docs
description: Viene descritto come creare e gestire un cluster Kubernetes nel dispositivo GPU Pro Azure Stack Edge tramite l'interfaccia di Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: cb783e5da7364f38944ce31ce49a6a6529658fe3
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90903216"
---
# <a name="connect-to-and-manage-a-kubernetes-cluster-via-kubectl-on-your-azure-stack-edge-pro-gpu-device"></a>Connettersi a un cluster Kubernetes e gestirlo tramite kubectl nel dispositivo GPU Pro Azure Stack Edge

Nel dispositivo Azure Stack Edge Pro viene creato un cluster Kubernetes quando si configura il ruolo di calcolo. Una volta creato il cluster Kubernetes, è possibile connettersi al cluster e gestirlo localmente da un computer client tramite uno strumento nativo, ad esempio *kubectl*.

Questo articolo descrive come connettersi a un cluster Kubernetes nel dispositivo Azure Stack Edge Pro e quindi gestirlo con *kubectl*. 


## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare che:

1. È possibile accedere a un dispositivo Azure Stack Edge Pro.

2. Aver attivato il dispositivo Azure Stack Edge Pro come descritto in [Attivare il dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md).

3. Il ruolo di calcolo è stato abilitato nel dispositivo. Un cluster Kubernetes è stato creato anche nel dispositivo quando è stato configurato il calcolo nel dispositivo in base alle istruzioni riportate in [configurare il calcolo nel dispositivo Azure stack Edge Pro](azure-stack-edge-gpu-deploy-configure-compute.md).

4. Per accedere al dispositivo, è possibile accedere a un sistema client Windows che esegue PowerShell 5,0 o versione successiva. È possibile avere anche un altro client con un [sistema operativo supportato](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) . 

5. L'endpoint dell'API Kubernetes è presente nella pagina **dispositivo** dell'interfaccia utente Web locale. Per altre informazioni, vedere le istruzioni in [ottenere l'endpoint dell'API Kubernetes](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints)


## <a name="connect-to-powershell-interface"></a>Connettersi all'interfaccia di PowerShell

Dopo aver creato il cluster Kubernetes, è possibile accedere a questo cluster per creare spazi dei nomi e utenti e assegnare utenti agli spazi dei nomi. Sarà necessario connettersi all'interfaccia di PowerShell del dispositivo. Seguire questa procedura nel client Windows che esegue PowerShell.

[!INCLUDE [Connect to admin runspace](../../includes/azure-stack-edge-gateway-connect-minishell.md)]


## <a name="configure-cluster-access-via-rbac"></a>Configurare l'accesso al cluster tramite RBAC

Dopo aver creato il cluster Kubernetes, è possibile usare *kubectl* tramite cmdline per accedere al cluster. 

In questo approccio vengono creati uno spazio dei nomi e un utente. Quindi si associa l'utente allo spazio dei nomi. È anche necessario ottenere il file di *configurazione* che consente di usare un client Kubernetes per comunicare direttamente con il cluster Kubernetes creato senza dover connettersi all'interfaccia di PowerShell del dispositivo Azure stack Edge Pro.

1. Creare uno spazio dei nomi Digitare:

    `New-HcsKubernetesNamespace -Namespace <string>` 

    > [!NOTE]
    > Per i nomi di spazio dei nomi e utente, vengono applicate le [convenzioni di denominazione del sottodominio DNS](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-subdomain-names) .

    Di seguito è riportato un output di esempio:

    `[10.100.10.10]: PS> New-HcsKubernetesNamespace -Namespace "myasetest1"`

2. Creare un utente e ottenere un file di configurazione. Digitare:

    `New-HcsKubernetesUser -UserName <string>`

    > [!NOTE]
    > Non è possibile usare *aseuser* come nome utente perché è riservato per un utente predefinito associato allo spazio dei nomi internet per Azure stack Edge Pro.

    Di seguito è riportato un esempio di output del file di configurazione:
   
    ```powershell
    [10.100.10.10]: PS> New-HcsKubernetesUser -UserName "aseuser1"
        apiVersion: v1
        clusters:
        - cluster:
            certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJd01ERXlPVEUyTlRFeE4xb1hEVE13TURFeU5qRTJOVEV4TjFvd0ZURVRNQkVHQTFVRQpBeE1LYTNWaVpYSnVaWFJsY3pDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBTXNpCkdZUHB0U1VWcDhKZEdVcHE1MVBURWhsZm8wWkk3YXFBdUlrOHZWdUFCZHJQK1FBSzFxcEN1di93NjIwbUtpZ0QKak1aT3Q4QkREREppWHF6UDZRZm5Oc0U2VXBHMnh0YnYrcTZHV2R5K0t6WkxMbXlwWGY3VjlzZEJnejVKVDNvYQpIdzFja2NTUklHSlV3UWxTbklNaHJUS3JUNDZFUUp3d282TmlNUzZMZDZieVk3WkUrTGg3OS9aNEhLanhTRmhMClc5ZG8veThZR3FXUDZmZTFmMmVmSkhUeGtwR05HZE1UVjNuOFlCZ0pSRzdrNjh0N2MrZ1NhbUlVWVJpTUNSNFAKYlFxcFpscWYvV2REZEJHOFh6aDJ0M1l4SkVIMm00T0Z1cSsvUitMYm95aHdKbmNMdVJ5OEpNZWlwTEQ3UlN0QwpZTDNNR0EzN2JieTRyYm4zVzg4Q0F3RUFBYU1qTUNFd0RnWURWUjBQQVFIL0JBUURBZ0trTUE4R0ExVWRFd0VCCi93UUZNQU1CQWY4d0RRWUpLb1pJaHZjTkFRRUxCUUFEZ2dFQkFNbzFwWlBtQzV1cmRPZUJhSWQ4eEQzRkxCMG8KTlErbXBXMWpDd0ZtY3h6dUtlWmRsNXc2N0tuS2JTcDR0TXo1cXg3bUtSc0UxcnBoWkh2VHlKUXg1ZFk2ZE1Kdgp5d2FQZjBpT05TNlU2cC9INE12U1dJaEtJZ1FuTnE1dDh4TjJCNnZpQW41RmZoRkx6WEQrUlZGSm42cnovWkZnCmV6MHpxTkNKYmcvelFucFROcmQ2cnFFRHpoSVFZOVdYVWQycFh3ZXRqUXJpMkpZamh4NmtEcTVoRkZTM0FLUnIKOWlQTVQxaWNkR1NUMFVvM1hIZ1k2ck45WGp3MHFrY2I0Sy83UlVVWlRvS3dKamROR3lNTnpad000L2puR0p5SwpQTE9ycU5Ddlkvb0lkVEM5eVZVY3VRbXVlR0VqT20xUnN1RDFHYVE0RTZwakppVWJpMVdrajJ1bFhOWT0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
            server: https://10.128.47.90:6443
            name: kubernetes
        contexts:
        - context:
            cluster: kubernetes
            user: aseuser1
            name: aseuser1@kubernetes
        current-context: aseuser1@kubernetes
        kind: Config
        preferences: {}
        users:
        - name: aseuser1
            user:
            client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMwVENDQWJtZ0F3SUJBZ0lJWlFXcjY2cGFWSm93RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TURBeE1qa3hOalV4TVRkYUZ3MHlNVEF4TWpneU1qVTJNVGRhTUJNeApFVEFQQmdOVkJBTVRDR0Z6WlhWelpYSXhNSUlCSWpBTkJna3Foa2lHOXcwQkFRRUZBQU9DQVE4QU1JSUJDZ0tDCkFRRUF4R3RDaXJ2cVhGYm5wVmtaYlVPRWQ3cXg2UzNVZ092MlhHRHNKM2VYWXN0bUxQVjMrQnlBcWwyN211L1AKaWdmaWt6MG9QSW1iYmtvcVJkamlYckxFZnk0N3dHcEhzdUhUOHNLY2tHTnJleFE2bXhaZ29xaU1nL2FuMUpMdwpiOFEvVnlQaWdVdUt6eVBseEhUZmlmSVM5MzR1VnZVZUc0dzlMRjAyZ2s2Nitpc0ZtanhsVmhseWRMNlc2UmZTCjl0OGpNMEFkdEpJL0xNbE13RHJJRVdFKzM4WDVNelJhQkJYNnlzNDFWSkZxekcwdW14dHdxN2pGOXp1UTE4ekIKalRZaDl3OWVKcDJwS2Fvak5tNE9SSDh4SzVSaUhocjJ2anFJWXkxRDd2WDh0b0U1K05HNmxHZjh5L1NvQnNRbQpmOG9vL1k3SEZmQXVGdlN6WUc1RUlQTFM4UUlEQVFBQm95Y3dKVEFPQmdOVkhROEJBZjhFQkFNQ0JhQXdFd1lEClZSMGxCQXd3Q2dZSUt3WUJCUVVIQXdJd0RRWUpLb1pJaHZjTkFRRUxCUUFEZ2dFQkFNR1BxY0YzS1BCbHZ0K24KN1NOcGE3anhWYkhZVGxyNTgwVWxzek93WEwwVnVPUUlCYmN2djIzZk9HNkhDZlQ0bWxBU0JRWVNZcmpLMjJTTwpTWld4cjNQUDlhVzNHajkxc0ttSnc1ZUF1WFhQbUJpK1RWQzBvY0ZLaEQvZ0o1aC93YnBaVndpVjVyRWE5Kzc2CnhNcFAzRld6dG5tT1hPaEl6UFNlR3B4YWpwQXd3ZXd4QU0yb0xGRFZFcy9XTFFMODJZM3NFcE93NVNaSVJJNXMKUHhMUTVnV1ZPM2x2SXcwZ3IrdkJlanZSOUZKaWVuTWFRdGdjSVgyRmpDaDBRMHVYRkdsTVNXWEljbjRLRTR0TApQSFFMalRSVUwyVnRXcW1YZ1RBM3RzN01DcGNRTFdPZFJUYkpSejZCbkc1aXVwcDdOSlFvYW9YcWpNVk5DVDZCCllYMEd0Skk9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
            client-key-data: LS0tLS1CRUdJTiBSU0EgUFJJVkFURSBLRVktLS0tLQpNSUlFb2dJQkFBS0NBUUVBeEd0Q2lydnFYRmJucFZrWmJVT0VkN3F4NlMzVWdPdjJYR0RzSjNlWFlzdG1MUFYzCitCeUFxbDI3bXUvUGlnZmlrejBvUEltYmJrb3FSZGppWHJMRWZ5NDd3R3BIc3VIVDhzS2NrR05yZXhRNm14WmcKb3FpTWcvYW4xSkx3YjhRL1Z5UGlnVXVLenlQbHhIVGZpZklTOTM0dVZ2VWVHNHc5TEYwMmdrNjYraXNGbWp4bApWaGx5ZEw2VzZSZlM5dDhqTTBBZHRKSS9MTWxNd0RySUVXRSszOFg1TXpSYUJCWDZ5czQxVkpGcXpHMHVteHR3CnE3akY5enVRMTh6QmpUWWg5dzllSnAycEthb2pObTRPUkg4eEs1UmlIaHIydmpxSVl5MUQ3dlg4dG9FNStORzYKbEdmOHkvU29Cc1FtZjhvby9ZN0hGZkF1RnZTellHNUVJUExTOFFJREFRQUJBb0lCQUVVSUVXM2kxMTQycU5raQo5RjNEWWZZV1pscTJZYjRoc0FjTmhWSGxwUTN5d0dsQ3FEUktDQ3BZSVF3MkJqSFR6WnpEM0xWU0E0K0NmMUxuCkE4QVdnaHJVcStsWE1QVzhpcG9DTGJaTlNzUUord0x3bld2dFl0MHFQaGZtd0p2M1UrK1RUQkwyOHNVVUw3ZVkKLzh0aWlhbno3ZU5mNklIMENyZmgxcnQ3WWhsemtRd1hBVHNScVJja0dMaTgrdGN5WnVzdGFhbENUSzBGRTdCaQpBUGE5a2w1SG56eCs4TTcvNWladHkwTUIxYWpWMnlGblBkUmlKSFVCb1AxVVV0QUthYjVZU0RvNllkZ2pIUTRHCjNWN1l1YWZobnVFMXA0VVIvUkloVVdjRlVVaTFBOFpZMFdnd1BDTmhnMWpQZU5vb2Y1UHpRbEY1OTRBREVwUUYKOFR2bG92RUNnWUVBOWZZbUxyY0tlQ0JiMTFoQVhoTi91Z1RTbU5xNnpFL1pPSWl6M0xwckdjRDhvWDdCVW9GcgplelkxbktSS2tkczE5OERnVjlQZUhuNzllQTRoMjM5RkIwNFFhMUJBdUVMRzRsdHJ3VlNxaFBENUR6YkcrSEhSCnJtYThVMEpUSmVVS0tJVjRUUGxlTzFtK2tjbkRJVXY1ckpwZDVXU3RvcUhXdk9RZkEvRUF0VlVDZ1lFQXpHOTcKTitCZVVvbFNiREttVUNGdTdPZGhYSXJYR3RnSEorZ2JOMDlnSHRURG5PY0IxZ1NzNkpZa1FPQU9qbWFxK05lRAp5SUF1NytheWlFRmpyT2tzTGhkSTREUXNkWFZveFFGVko1V1JwWlk3UTVRaFZpYUR2enR4NDlzSDlKSkplM2U0Cnl3NWdpNGkxKy90MnY2eWRKcWdNQ0xxOHlEdFRrcE9PSitkbkp5MENnWUJwZ3lpcURaZU9KTU9CUTdpSkl2QSsKQ21lVmJ1K0hTaEd6TU9HSHBPamc2V3IybEh1Mk94S3lqblM5TjdWTmtLNDhGQitwVFpnUm1RUi9CZ0Q4T2tLUQplYXFOZnFYazViQ1AxZ3dKcVpwazRVTFdoZmNoQ1NLY0lESlZ2VFFTSTRrU0RQK29kYWs0Nkt6WnVhWGRtTXdJCmdVZ2FhZkFhdmpaeVhhSDRmT0NDNlFLQmdHVXJCaDh3dVh5KzJEc1RGWnF4OE9McjNoS2Q0clUyRXRSODJIc1cKbk1xbEgraVZxU0x3VFdFTWJBUnUzTVU3cVlCYnBxdWlRNWdVNG1UcmR4Z3FpK0tEUTEwd2RJL3IrbDBEdTlCTApCRGlkajlaeGg4M0tZWWhSTXBzLzJULys1TDVsRU4zcnozczl2RkZtcisxS3pycENqeklDdDBtZmtrd0hHV0pGCjhaWkJBb0dBVXB3aUIrcWlHbkpxU1FtZHNSZFVabGFBaTRpbGhaa01RYTRHem95ZFQ3OTVHTm44ZThBRjd3WHMKTGpyYjdEV1FwakdCMnZpUlkySUZBVmIyKzZsdDlwOVJRMTZnSmxpNU5ZRXVvQWRoWXBsVWdBZGFHWHNGNHdabwo3SHFHTHBGdmUxVU5Gb0dQdkxpWUNrUFVYdGduQ3dNb0R2SEpKNzVYMXl6ckh6cmxUS1k9Ci0tLS0tRU5EIFJTQSBQUklWQVRFIEtFWS0tLS0tCg==  
        
    [10.100.10.10]: PS>
    ```
    

3. Un file di configurazione viene visualizzato in testo normale. Copiare questo file e salvarlo come file di *configurazione* . 

    > [!IMPORTANT]
    > Non salvare il file di configurazione come file con estensione *txt* , salvare il file senza alcuna estensione di file.

4. Il file di configurazione deve risiedere nella `.kube` cartella del profilo utente nel computer locale. Copiare il file nella cartella nel profilo utente.

    ![Percorso del file di configurazione nel client](media/azure-stack-edge-j-series-create-kubernetes-cluster/location-config-file.png)

5. Associare lo spazio dei nomi all'utente creato. Digitare:

    `Grant-HcsKubernetesNamespaceAccess -Namespace <string> -UserName <string>`

    Di seguito è riportato un output di esempio:

    `[10.100.10.10]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace "myasetest1" -UserName "aseuser1"`

    Una volta ottenuto il file di configurazione, non è necessario l'accesso fisico al cluster. Se il client può effettuare il ping dell'indirizzo IP del dispositivo Azure Stack Edge Pro, dovrebbe essere possibile indirizzare il cluster usando i comandi *kubectl* .

6. Avviare una nuova sessione di PowerShell nel client. Non è necessario essere connessi all'interfaccia del dispositivo. È ora possibile installare `kubectl` nel client usando il comando seguente:

    ```powershell
    PS C:\windows\system32> curl https://storage.googleapis.com/kubernetes-release/release/v1.15.2/bin/windows/amd64/kubectl.exe -O kubectl.exe
    
    PS C:\windows\system32>
    ```
    Ad esempio, se il nodo master Kubernetes esegue v 1.15.2, installare v 1.15.2 nel client.

    > [!IMPORTANT]
    > Scaricare un client in cui non è presente più di una versione secondaria dal master. La versione del client, ma può condurre il master fino a una versione secondaria. Ad esempio, un Master v 1.3 dovrebbe funzionare con i nodi v 1.1, v 1.2 e v 1.3 e dovrebbe funzionare con i client v 1.2, v 1.3 e v 1.4. Per altre informazioni sulla versione del client Kubernetes, vedere [criteri di supporto per versione e sfasamento](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-version-skew)della versione di Kubernetes. Per ulteriori informazioni sulla versione del server Kubernetes in Azure Stack Edge Pro, vedere la pagina relativa alla versione del server Kubernetes.<!-- insert link-->
    > In alcuni casi, `kubectl` è preinstallato nel sistema se si esegue Docker per Windows o altri strumenti. È importante scaricare la versione specifica di `kubectl` come indicato in questa sezione per usare questo cluster kubernetes. 

    L'installazione richiede alcuni minuti.

7. Verificare che la versione installata sia quella scaricata. È necessario specificare il percorso assoluto in cui `kubectl.exe` è stato installato nel sistema.
    
    ```powershell
    PS C:\Users\myuser> C:\windows\system32\kubectl.exe version
    Client Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.2", GitCommit:"f6278300bebbb750328ac16ee6dd3aa7d3549568", GitTreeState:"clean", BuildDate:"2019-08-05T09:23:26Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"windows/amd64"}
    Server Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.1", GitCommit:"4485c6f18cee9a5d3c3b4e523bd27972b1b53892", GitTreeState:"clean", BuildDate:"2019-07-18T09:09:21Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"linux/amd64"}
    PS C:\Users\myuser>
    ``` 
    
    Per altre informazioni sui `kubectl` comandi usati per gestire il cluster Kubernetes, vedere [Panoramica di kubectl](https://kubernetes.io/docs/reference/kubectl/overview/).

8. Aggiungere una voce DNS al file hosts nel sistema. 

    1. Eseguire blocco note come amministratore e aprire il `hosts` file che si trova in `C:\windows\system32\drivers\etc\hosts` . 
    2. Usare le informazioni salvate dalla pagina **dispositivo** nell'interfaccia utente locale nel passaggio precedente per creare la voce nel file degli host. 

        Ad esempio, copiare questo endpoint `https://compute.asedevice.microsoftdatabox.com/[10.100.10.10]` per creare la voce seguente con l'indirizzo IP del dispositivo e il dominio DNS: 

        `10.100.10.10 compute.asedevice.microsoftdatabox.com`

9. Per verificare che sia possibile connettersi ai pod Kubernetes, digitare:
    
    ```powershell
    PS C:\Users\myuser> kubectl get pods -n "myasetest1"
    No resources found.
    PS C:\Users\myuser>
    ```
È ora possibile distribuire le applicazioni nello spazio dei nomi, quindi visualizzare le applicazioni e i relativi log.

> [!IMPORTANT]   
> Sono disponibili molti comandi che non saranno in grado di eseguire, ad esempio i comandi che richiedono l'accesso amministrativo. È possibile eseguire solo operazioni consentite nello spazio dei nomi.


## <a name="remove-kubernetes-cluster"></a>Rimuovere il cluster Kubernetes

Per rimuovere il cluster Kubernetes, sarà necessario rimuovere la configurazione di calcolo.

Per istruzioni dettagliate, vedere [rimuovere la configurazione di calcolo](azure-stack-edge-j-series-manage-compute.md#remove-compute-configuration).
   

## <a name="next-steps"></a>Passaggi successivi

- [Distribuire un'applicazione senza stato nel Azure stack Edge Pro](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md).
