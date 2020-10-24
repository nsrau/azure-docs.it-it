---
title: Aggiornare un cluster per l'uso del nome comune del certificato
description: Informazioni su come convertire un certificato del cluster di Service Fabric di Azure da dichiarazioni basate su identificazione personale a nomi comuni.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 013b8190390a4b05791b0a56072487f249956ec5
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92495208"
---
# <a name="convert-cluster-certificates-from-thumbprint-based-declarations-to-common-names"></a>Convertire i certificati del cluster da dichiarazioni basate su identificazione personale a nomi comuni

La firma di un certificato (comunemente nota come identificazione personale) è univoca. Un certificato di cluster dichiarato da identificazione personale fa riferimento a un'istanza specifica di un certificato. Questa specificità rende il rollover e la gestione dei certificati in generale, difficile ed esplicito. Ogni modifica richiede l'orchestrazione degli aggiornamenti del cluster e degli host di elaborazione sottostanti.

La conversione di una dichiarazione di certificato del cluster Service Fabric di Azure da un'identificazione personale basata sul nome comune del soggetto (CN) del certificato semplifica notevolmente la gestione. In particolare, il rollover di un certificato non richiede più un aggiornamento del cluster. Questo articolo descrive come convertire un cluster esistente in dichiarazioni basate su CN senza tempi di inattività.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="move-to-certificate-authority-signed-certificates"></a>Passa a certificati firmati dall'autorità di certificazione

La sicurezza di un cluster il cui certificato è dichiarato dall'identificazione personale si basa sul fatto che non è possibile, o non è possibile eseguire il calcolo, per forgiare un certificato con la stessa firma di un altro. In questo caso, la provenienza del certificato è meno importante, quindi i certificati autofirmati sono appropriati.

Al contrario, la sicurezza di un cluster i cui certificati sono dichiarati da CN fluisce dal trust implicito del proprietario del cluster nel provider di certificati. Il provider è il servizio di infrastruttura a chiave pubblica (PKI) che ha emesso il certificato. L'attendibilità si basa, tra gli altri fattori, sulle procedure di certificazione dell'infrastruttura a chiave pubblica (PKI), che la sicurezza operativa venga controllata e approvata da altre parti attendibili e così via.

Il proprietario del cluster deve anche avere una conoscenza approfondita delle autorità di certificazione che inviano i propri certificati, poiché si tratta di un aspetto fondamentale della convalida dei certificati in base all'oggetto. Questo implica anche che i certificati autofirmati non sono completamente idonei a questo scopo. Letteralmente chiunque può generare un certificato con un oggetto specifico.

Un certificato dichiarato da CN viene in genere considerato valido se:

* La catena può essere compilata correttamente.
* L'oggetto ha l'elemento CN previsto.
* L'emittente (immediato o superiore nella catena) è considerato attendibile dall'agente che esegue la convalida.

Service Fabric supporta la dichiarazione di certificati da CN in due modi:

* Con autorità emittenti *implicite* , il che significa che la catena deve terminare con un trust anchor.
* Con autorità emittenti dichiarate dall'identificazione personale, nota come blocco dell'emittente.

Per altre informazioni, vedere [dichiarazioni di convalida dei certificati basate sul nome comune](cluster-security-certificates.md#common-name-based-certificate-validation-declarations).

Per convertire un cluster usando un certificato autofirmato dichiarato da identificazione personale in CN, il certificato firmato da un'autorità di certificazione di destinazione deve essere introdotto per la prima volta nel cluster tramite identificazione personale. Solo in questo modo è possibile eseguire la conversione dall'identificazione personale al CN.

A scopo di test, un certificato autofirmato *può* essere dichiarato da CN, ma solo se l'emittente è stato aggiunto alla relativa identificazione personale. Dal punto di vista della sicurezza, questa azione è quasi equivalente alla dichiarazione dello stesso certificato per identificazione personale. Una conversione corretta di questo tipo non garantisce una conversione corretta dall'identificazione personale a CN con un certificato firmato da un'autorità di certificazione. Si consiglia di eseguire il test della conversione con un certificato firmato CA appropriato. Per questo test sono disponibili opzioni gratuite.

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>Caricare il certificato e installarlo nel set di scalabilità

In Azure, il meccanismo consigliato per ottenere e provisioning dei certificati riguarda Azure Key Vault e i relativi strumenti. È necessario eseguire il provisioning di un certificato che corrisponde alla dichiarazione del certificato cluster in ogni nodo dei set di scalabilità di macchine virtuali che costituiscono il cluster. Per altre informazioni, vedere [segreti nei set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-securely-ship-a-certificate-to-the-vm).

È importante installare i certificati cluster correnti e di destinazione nelle macchine virtuali di ogni tipo di nodo del cluster prima di apportare modifiche alle dichiarazioni di certificato del cluster. Il percorso di rilascio del certificato per il provisioning in un nodo Service Fabric viene discusso in modo approfondito nel [percorso di un certificato](cluster-security-certificate-management.md#the-journey-of-a-certificate).

## <a name="bring-the-cluster-to-an-optimal-starting-state"></a>Portare il cluster a uno stato di avvio ottimale

Conversione di una dichiarazione di certificato da un effetto basato sull'identificazione personale a quello basato su CN:

- Il modo in cui ogni nodo del cluster rileva e presenta le proprie credenziali ad altri nodi.
- In che modo ogni nodo convalida le credenziali della controparte quando stabilisce una connessione protetta.

Prima di procedere, esaminare le [regole di presentazione e di convalida per entrambe le configurazioni](cluster-security-certificates.md#certificate-configuration-rules) . La considerazione più importante quando si esegue una conversione da identificazione personale a CN è che i nodi aggiornati e non ancora aggiornati, ovvero i nodi appartenenti a domini di aggiornamento diversi, devono essere in grado di eseguire correttamente l'autenticazione reciproca in qualsiasi momento durante l'aggiornamento. Il modo consigliato per ottenere questo comportamento consiste nel dichiarare il certificato di destinazione o obiettivo tramite identificazione personale in un aggiornamento iniziale. Completare quindi la transizione a CN in una successiva. Se il cluster si trova già in uno stato di avvio consigliato, è possibile ignorare questa sezione.

Sono disponibili più Stati di avvio validi per una conversione. L'invariante è che il cluster sta già utilizzando il certificato di destinazione (dichiarato dall'identificazione personale) all'inizio dell'aggiornamento a CN. Si considerino `GoalCert` , `OldCert1` e `OldCert2` in questo articolo.

#### <a name="valid-starting-states"></a>Stati di avvio validi

- `Thumbprint: GoalCert, ThumbprintSecondary: None`
- `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1`, dove `GoalCert` ha una data successiva a `NotAfter` quella di `OldCert1`
- `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert`, dove `GoalCert` ha una data successiva a `NotAfter` quella di `OldCert1`

Se il cluster non è in uno degli stati validi descritti in precedenza, vedere le informazioni su come ottenere tale stato nella sezione alla fine di questo articolo.

## <a name="select-the-desired-cn-based-certificate-validation-scheme"></a>Selezionare lo schema di convalida del certificato basato su CN desiderato

Come descritto in precedenza, Service Fabric supporta la dichiarazione di certificati da parte di CN con un trust anchor implicito o con il blocco esplicito delle identificazioni personali dell'autorità emittente. Per altre informazioni, vedere [dichiarazioni di convalida dei certificati basate sul nome comune](cluster-security-certificates.md#common-name-based-certificate-validation-declarations).

Assicurarsi di avere una corretta conoscenza delle differenze e delle implicazioni della scelta di uno dei due meccanismi. Sintatticamente questa differenza o scelta è determinata dal valore del `certificateIssuerThumbprintList` parametro. Empty significa basarsi su una CA radice attendibile (Trust Anchor), mentre un set di identificazioni personali limita le autorità emittenti dirette consentite dei certificati del cluster.

   > [!NOTE]
   > Il `certificateIssuerThumbprint` campo consente di specificare le autorità emittenti dirette previste per i certificati dichiarati dal CN dell'oggetto. I valori accettabili sono una o più identificazioni personali SHA1 separate da virgole. Questa azione rafforza la convalida del certificato.
   >
   > Se non viene specificata alcuna autorità emittente oppure l'elenco è vuoto, il certificato verrà accettato per l'autenticazione se la catena può essere compilata. Il certificato finisce quindi in una radice considerata attendibile dal validator. Se vengono specificate una o più identificazioni personali dell'autorità emittente, il certificato verrà accettato se l'identificazione personale dell'emittente diretta, come Estratto dalla catena, corrisponde a uno qualsiasi dei valori specificati in questo campo. Il certificato verrà accettato se la radice è attendibile o meno.
   >
   > Un'infrastruttura a chiave pubblica può utilizzare autorità di certificazione diverse (note anche come autorità *emittenti*) per firmare i certificati con un oggetto specifico. Per questo motivo, è importante specificare tutte le identificazioni personali dell'autorità emittente previste per tale oggetto. In altre parole, non è garantito che il rinnovo di un certificato sia firmato dallo stesso emittente del certificato da rinnovare.
   >
   > La specifica dell'emittente è considerata una procedura consigliata. L'omissione dell'emittente continuerà a funzionare per i certificati concatenati a una radice attendibile, ma questo comportamento presenta alcune limitazioni e potrebbe essere suddiviso in più fasi nel prossimo futuro. I cluster distribuiti in Azure, protetti con certificati X509 emessi da un'infrastruttura a chiave pubblica privata e dichiarati da Subject potrebbero non essere convalidati da Service Fabric (per la comunicazione da cluster a servizio). Per la convalida è necessario che i criteri dei certificati dell'infrastruttura a chiave pubblica siano individuabili, disponibili e accessibili.

## <a name="update-the-clusters-azure-resource-manager-template-and-deploy"></a>Aggiornare il modello di Azure Resource Manager del cluster e distribuire

Gestire i cluster di Service Fabric con i modelli di Azure Resource Manager (ARM). Un'alternativa, che usa anche artefatti JSON, è la [Azure Resource Explorer (anteprima)](https://resources.azure.com). Al momento non è disponibile un'esperienza equivalente nel portale di Azure.

Se il modello originale corrispondente a un cluster esistente non è disponibile, è possibile ottenere un modello equivalente nel portale di Azure. Passare al gruppo di risorse che contiene il cluster e selezionare **Esporta modello** dal menu **automazione** a sinistra. Quindi selezionare le risorse desiderate. Come minimo, le risorse del set di scalabilità di macchine virtuali e del cluster devono essere esportate. Il modello generato può essere scaricato anche. Questo modello potrebbe richiedere modifiche prima che sia completamente distribuibile. Anche il modello potrebbe non corrispondere esattamente a quello originale. Si tratta di un riflesso dello stato corrente della risorsa cluster.

Di seguito sono riportate le modifiche necessarie:

- Aggiornamento della definizione dell'estensione del nodo Service Fabric (nella risorsa della macchina virtuale). Se il cluster definisce più tipi di nodo, sarà necessario aggiornare la definizione di ogni set di scalabilità di macchine virtuali corrispondente.
- Aggiornamento della definizione della risorsa cluster.

Esempi dettagliati sono inclusi qui.

### <a name="update-the-virtual-machine-scale-set-resources"></a>Aggiornare le risorse del set di scalabilità di macchine virtuali
Da:
```json
"virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                            ...
                        },
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            ...
                            "certificate": {
                                "thumbprint": "[parameters('certificateThumbprint')]",
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            }
                        },
                        ...
                    }
                },
```
Con:
```json
"virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                            ...
                        },
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            ...
                            "certificate": {
                                "commonNames": [
                                    "[parameters('certificateCommonName')]"
                                ],
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            }
                        },
                        ...
                    }
                },
```

### <a name="update-the-cluster-resource"></a>Aggiornare la risorsa cluster

Nella risorsa **Microsoft. ServiceFabric/Clusters** aggiungere una proprietà **certificateCommonNames** con un'impostazione **commonNames** e rimuovere completamente la proprietà **Certificate** (tutte le impostazioni).

Da:
```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            ...
        ],
        "properties": {
            "addonFeatures": [
                ...
            ],
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
            },
        ...
```
Con:
```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            ...
        ],
        "properties": {
            "addonFeatures": [
                ...
            ],
            "certificateCommonNames": {
                "commonNames": [
                    {
                        "certificateCommonName": "[parameters('certificateCommonName')]",
                        "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprintList')]"
                    }
                ],
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
        ...
```

Per ulteriori informazioni, vedere la pagina relativa alla [distribuzione di un cluster Service fabric che utilizza il nome comune del certificato anziché l'identificazione personale](./service-fabric-create-cluster-using-cert-cn.md).

## <a name="deploy-the-updated-template"></a>Distribuire il modello aggiornato

Ridistribuire il modello aggiornato dopo avere apportato le modifiche.

```powershell
$groupname = "sfclustertutorialgroup"

New-AzResourceGroupDeployment -ResourceGroupName $groupname -Verbose `
    -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" 
```

## <a name="achieve-a-valid-starting-state-for-converting-a-cluster-to-cn-based-certificate-declarations"></a>Ottenere uno stato iniziale valido per la conversione di un cluster in dichiarazioni di certificati basate su CN

| Stato iniziale | Aggiornamento 1 | Aggiornamento 2 |
| :--- | :--- | :--- |
| `Thumbprint: OldCert1, ThumbprintSecondary: None`e `GoalCert` con una data successiva a `NotAfter``OldCert1` | `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert` | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: None`e `OldCert1` con una data successiva a `NotAfter``GoalCert` | `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1` | `Thumbprint: GoalCert, ThumbprintSecondary: None` |
| `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert`, dove `OldCert1` ha una data successiva a `NotAfter``GoalCert` | Eseguire l'aggiornamento a `Thumbprint: GoalCert, ThumbprintSecondary: None` | - |
| `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1`, dove `OldCert1` ha una data successiva a `NotAfter``GoalCert` | Eseguire l'aggiornamento a `Thumbprint: GoalCert, ThumbprintSecondary: None` | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: OldCert2` | Rimuovere uno dei `OldCert1` o `OldCert2` per ottenere lo stato `Thumbprint: OldCertx, ThumbprintSecondary: None` | Continua dal nuovo stato iniziale |

Per istruzioni su come eseguire uno di questi aggiornamenti, vedere gestire i [certificati in un cluster di Azure Service Fabric](service-fabric-cluster-security-update-certs-azure.md).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulla [sicurezza del cluster](service-fabric-cluster-security.md).
* Informazioni su come eseguire [il rollup di un certificato del cluster in base al nome comune](service-fabric-cluster-rollover-cert-cn.md).
* Informazioni su come [configurare un cluster per il rollover](cluster-security-certificate-management.md)automatico non semplice.

[image1]: ./media/service-fabric-cluster-change-cert-thumbprint-to-cn/PortalViewTemplates.png
