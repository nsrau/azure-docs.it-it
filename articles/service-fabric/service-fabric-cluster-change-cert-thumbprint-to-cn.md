---
title: Aggiornare un cluster per l'uso del nome comune del certificato
description: Informazioni su come convertire un certificato di Service Fabric cluster da dichiarazioni basate su identificazione personale a nomi comuni.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 224798565921593d3c91dfcc187efa71a71b1fdd
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92368061"
---
# <a name="convert-cluster-certificates-from-thumbprint-based-declarations-to-common-names"></a>Convertire i certificati del cluster da dichiarazioni basate su identificazione personale a nomi comuni
La firma di un certificato (colloquialmente noto come "identificazione personale") è univoca, il che significa che un certificato del cluster dichiarato da identificazione personale fa riferimento a un'istanza specifica di un certificato. Questo, a sua volta, rende la gestione e il rollover del certificato, in generale, difficile ed esplicito: ogni modifica richiede l'orchestrazione degli aggiornamenti del cluster e degli host di elaborazione sottostanti. La conversione di una dichiarazione di certificato del cluster di Service Fabric da una dichiarazione basata sull'identificazione personale basata sul nome comune del soggetto del certificato semplifica notevolmente la gestione. in particolare, il rollover di un certificato non richiede più un aggiornamento del cluster. Questo articolo descrive come convertire un cluster esistente in dichiarazioni comuni basate sul nome senza tempi di inattività.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="moving-to-certificate-authority-ca-signed-certificates"></a>Passaggio ai certificati firmati dall'autorità di certificazione (CA)
La sicurezza di un cluster il cui certificato è dichiarato dall'identificazione personale si basa sul fatto che non è possibile o non è possibile eseguire il calcolo della falsificazione di un certificato con la stessa firma di un altro. In questo caso, la provenienza del certificato è meno importante, quindi i certificati autofirmati sono appropriati. Al contrario, la sicurezza di un cluster con certificati dichiarati dal nome comune deriva dal servizio di infrastruttura a chiave pubblica (PKI) che ha emesso il certificato e include aspetti quali le procedure di certificazione, se la sicurezza operativa viene controllata e molti altri. Per questo motivo, la scelta di un'infrastruttura a chiave pubblica è importante, una conoscenza approfondita delle autorità emittenti (autorità di certificazione o CA) è obbligatoria e i certificati autofirmati sono essenzialmente inutili. Un certificato dichiarato dal nome comune (CN) viene in genere considerato valido se la catena può essere compilata correttamente, l'oggetto ha l'elemento CN previsto e l'emittente (immediato o superiore nella catena) è considerato attendibile dall'agente che esegue la convalida. Service Fabric supporta la dichiarazione di certificati da parte del CN con l'emittente ' implicità (la catena deve terminare con un trust anchor) o con autorità emittenti dichiarate dall'identificazione personale ("blocco dell'autorità emittente"); per altri dettagli, vedere questo  [articolo](cluster-security-certificates.md#common-name-based-certificate-validation-declarations) . Per convertire un cluster usando un certificato autofirmato dichiarato da identificazione personale nel nome comune, la destinazione, il certificato firmato da un'autorità di certificazione deve essere introdotto per la prima volta nel cluster mediante identificazione personale. solo in questo modo è possibile eseguire la conversione da TP a CN.

A scopo di test, un certificato autofirmato può essere dichiarato da CN, aggiungendo l'emittente alla relativa identificazione personale. dal punto di vista della sicurezza, questo è quasi equivalente alla dichiarazione dello stesso certificato da parte di TP. Si noti, tuttavia, che una conversione corretta di questo tipo non garantisce una conversione corretta da TP a CN con un certificato firmato da un'autorità di certificazione. È pertanto consigliabile testare la conversione con un certificato firmato da CA appropriato (sono disponibili opzioni gratuite).

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>Caricare il certificato e installarlo nel set di scalabilità
In Azure, il meccanismo consigliato per ottenere e provisioning dei certificati riguarda il servizio Azure Key Vault e i relativi strumenti. È necessario eseguire il provisioning di un certificato corrispondente alla dichiarazione del certificato cluster in ogni nodo dei set di scalabilità di macchine virtuali che comprende il cluster. Per ulteriori informazioni, fare riferimento ai [segreti nei set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-securely-ship-a-certificate-to-the-vm) . È importante che i certificati cluster correnti e di destinazione siano installati nelle macchine virtuali di ogni tipo di nodo del cluster prima di apportare modifiche alle dichiarazioni di certificato del cluster. Il percorso di rilascio del certificato per il provisioning in un nodo di Service Fabric viene illustrato in dettaglio [qui](cluster-security-certificate-management.md#the-journey-of-a-certificate).

## <a name="bring-cluster-to-an-optimal-starting-state"></a>Portare il cluster a uno stato di avvio ottimale
La conversione di una dichiarazione di certificato da un'identificazione personale a un effetto basato sul nome comune è:

- Come ogni nodo del cluster trova e presenta le proprie credenziali ad altri nodi
- Modalità di convalida delle credenziali della controparte in ogni nodo durante la creazione di una connessione protetta  

Prima di procedere, esaminare le [regole di presentazione e di convalida per entrambe le configurazioni](cluster-security-certificates.md#certificate-configuration-rules) . La considerazione più importante quando si esegue una conversione di nome da identificazione personale a comune è che i nodi aggiornati e non ancora aggiornati, ovvero i nodi appartenenti a domini di aggiornamento diversi, devono essere in grado di eseguire correttamente l'autenticazione reciproca in qualsiasi momento durante l'aggiornamento. Il metodo consigliato per ottenere questo risultato consiste nel dichiarare il certificato di destinazione/obiettivo in base all'identificazione personale in un aggiornamento iniziale e completare la transizione al nome comune in uno successivo. Se il cluster si trova già in uno stato di avvio consigliato, è possibile ignorare questa sezione.

Sono disponibili più Stati di avvio validi per una conversione. l'invariante è che il cluster sta già usando il certificato di destinazione (dichiarato da identificazione personale) all'inizio dell'aggiornamento al nome comune. Si considerino `GoalCert` , `OldCert1` , `OldCert2` :

#### <a name="valid-starting-states"></a>Stati di avvio validi
- `Thumbprint: GoalCert, ThumbprintSecondary: None`
- `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1`, dove `GoalCert` ha una data successiva a `NotAfter` quella di `OldCert1`
- `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert`, dove `GoalCert` ha una data successiva a `NotAfter` quella di `OldCert1`

Se il cluster non si trova in uno degli stati validi descritti in precedenza, fare riferimento all'appendice per ottenere tale stato alla fine di questo articolo.

## <a name="select-the-desired-common-name-based-certificate-validation-scheme"></a>Selezionare lo schema di convalida dei certificati basato sul nome comune desiderato
Come descritto in precedenza, Service Fabric supporta la dichiarazione di certificati da parte di CN con un trust anchor implicito o con il blocco esplicito delle identificazioni personali dell'autorità emittente. Per informazioni dettagliate, fare riferimento a [questo articolo](cluster-security-certificates.md#common-name-based-certificate-validation-declarations) e assicurarsi di avere una conoscenza corretta delle differenze e le implicazioni della scelta di uno dei due meccanismi. Sintatticamente, questa differenza/scelta è determinata dal valore del `certificateIssuerThumbprintList` parametro: Empty significa che si basa su una CA radice attendibile (Trust Anchor), mentre un set di identificazioni personali limita le emittenti dirette consentite dei certificati del cluster.

   > [!NOTE]
   > Il campo ' certificateIssuerThumbprint ' consente di specificare le autorità emittenti dirette previste per i certificati dichiarati dal nome comune del soggetto. I valori accettabili sono una o più identificazioni personali SHA1 separate da virgole. Si noti che si tratta di un potenziamento della convalida del certificato: se non è specificata alcuna autorità emittente o se l'elenco è vuoto, il certificato verrà accettato per l'autenticazione se la catena può essere compilata e termina in una radice considerata attendibile dal validator. Se vengono specificate una o più identificazioni personali dell'autorità emittente, il certificato verrà accettato se l'identificazione personale dell'emittente diretta, come Estratto dalla catena, corrisponde a uno qualsiasi dei valori specificati in questo campo, indipendentemente dal fatto che la radice sia attendibile o meno. Si noti che un'infrastruttura a chiave pubblica può utilizzare autorità di certificazione diverse (' emittenti ') per firmare i certificati con un oggetto specifico, quindi è importante specificare tutte le identificazioni personali dell'autorità emittente previste per tale oggetto. In altre parole, non è garantito che il rinnovo di un certificato sia firmato dallo stesso emittente del certificato da rinnovare.
   >
   > La procedura consigliata è quella di specificare l'autorità di certificazione. Anche se è ancora possibile ometterla senza compromettere il funzionamento (per certificati concatenati a una radice attendibile), questo comportamento è soggetto a limitazioni e, nell'immediato futuro, potrebbe essere eliminato. Si noti inoltre che i cluster distribuiti in Azure e protetti con certificati X509, emessi da un'infrastruttura a chiave pubblica privata e dichiarati dal soggetto, potrebbero non essere convalidati dal servizio Azure Service Fabric (per le comunicazioni dal cluster al servizio), se i criteri dei certificati dell'infrastruttura a chiave pubblica non sono rilevabili, disponibili e accessibili. 

## <a name="update-the-clusters-azure-resource-management-arm-template-and-deploy"></a>Aggiornare il modello di Azure Resource Manager (ARM) del cluster e distribuire
È consigliabile gestire i cluster di Azure Service Fabric con i modelli ARM; un'alternativa, anche usando gli artefatti JSON, è la [Azure Resource Explorer (anteprima)](https://resources.azure.com). Al momento non è disponibile un'esperienza equivalente nel portale di Azure. Se il modello originale corrispondente a un cluster esistente non è disponibile, è possibile ottenere un modello equivalente nella portale di Azure passando al gruppo di risorse che contiene il cluster, selezionando **Esporta modello** dal menu a sinistra di **automazione** e quindi selezionando altre risorse desiderate; come minimo, le risorse del set di scalabilità di macchine virtuali e del cluster devono essere esportate. Il modello generato può essere scaricato anche. Si noti che questo modello può richiedere modifiche prima che sia completamente distribuibile e potrebbe non corrispondere esattamente a quello originale. si tratta di una reflection dello stato corrente della risorsa cluster.

Di seguito sono riportate le modifiche necessarie:
    - aggiornamento della definizione dell'estensione del nodo Service Fabric (nella risorsa della macchina virtuale); Se il cluster definisce più tipi di nodo, sarà necessario aggiornare la definizione di ogni set di scalabilità di macchine virtuali corrispondente
    - aggiornamento della definizione di risorsa cluster

Di seguito sono elencati esempi dettagliati.

### <a name="updating-the-virtual-machine-scale-set-resources"></a>Aggiornamento delle risorse del set di scalabilità di macchine virtuali
Da
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
A
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

### <a name="updating-the-cluster-resource"></a>Aggiornamento della risorsa cluster
Nella risorsa **Microsoft. ServiceFabric/Clusters** aggiungere una proprietà **certificateCommonNames** con un'impostazione **commonNames** e rimuovere completamente la proprietà **Certificate** (tutte le impostazioni):

Da
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
A
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

Per ulteriori informazioni, vedere la pagina relativa alla [distribuzione di un cluster Service fabric che utilizza il nome comune del certificato anziché l'identificazione personale.](./service-fabric-create-cluster-using-cert-cn.md)

## <a name="deploy-the-updated-template"></a>Distribuire il modello aggiornato
Distribuire nuovamente il modello aggiornato dopo aver apportato le modifiche.

```powershell
$groupname = "sfclustertutorialgroup"

New-AzResourceGroupDeployment -ResourceGroupName $groupname -Verbose `
    -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" 
```

## <a name="appendix-achieve-a-valid-starting-state-for-converting-a-cluster-to-cn-based-certificate-declarations"></a>Appendice: ottenere uno stato iniziale valido per la conversione di un cluster in dichiarazioni di certificati basate su CN

| Stato iniziale | Aggiornamento 1 | Aggiornamento 2 |
| :--- | :--- | :--- |
| `Thumbprint: OldCert1, ThumbprintSecondary: None`e `GoalCert` con una data successiva a `NotAfter``OldCert1` | `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert` | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: None`e `OldCert1` con una data successiva a `NotAfter``GoalCert` | `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1` | `Thumbprint: GoalCert, ThumbprintSecondary: None` |
| `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert`, dove `OldCert1` ha una data successiva a `NotAfter``GoalCert` | Eseguire l'aggiornamento a `Thumbprint: GoalCert, ThumbprintSecondary: None` | - |
| `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1`, dove `OldCert1` ha una data successiva a `NotAfter``GoalCert` | Eseguire l'aggiornamento a `Thumbprint: GoalCert, ThumbprintSecondary: None` | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: OldCert2` | Rimuovere uno dei `OldCert1` o `OldCert2` per ottenere lo stato `Thumbprint: OldCertx, ThumbprintSecondary: None` | Continua dal nuovo stato iniziale |

Per istruzioni su come eseguire uno di questi aggiornamenti, vedere [questo documento](service-fabric-cluster-security-update-certs-azure.md).


## <a name="next-steps"></a>Passaggi successivi
* Informazioni sulla [sicurezza del cluster](service-fabric-cluster-security.md).
* Informazioni su come eseguire [il rollover di un certificato del cluster in base al nome comune](service-fabric-cluster-rollover-cert-cn.md)
* Informazioni su come [configurare un cluster per il rollover](cluster-security-certificate-management.md) automatico non semplice

[image1]: ./media/service-fabric-cluster-change-cert-thumbprint-to-cn/PortalViewTemplates.png
