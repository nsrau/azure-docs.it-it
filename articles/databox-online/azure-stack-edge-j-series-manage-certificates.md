---
title: Usare i certificati con Azure Stack GPU perimetrale | Microsoft Docs
description: Descrive l'uso dei certificati con Azure Stack dispositivo GPU perimetrale, inclusi i motivi per usare, i tipi e il modo in cui caricare i certificati nel dispositivo.
services: Azure Stack Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 7742d036857525ce6be64a53234f7aa717d4bdca
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2020
ms.locfileid: "89147083"
---
# <a name="use-certificates-with-azure-stack-edge-series"></a>Usare i certificati con la serie Azure Stack Edge 

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Questo articolo descrive i tipi di certificati che possono essere installati nel dispositivo Azure Stack Edge. Nell'articolo sono inoltre inclusi i dettagli di ogni tipo di certificato insieme alla procedura per l'installazione e l'identificazione della data di scadenza. 

## <a name="about-certificates"></a>Informazioni sui certificati

Un certificato fornisce un collegamento tra una **chiave pubblica** e un'entità, ad esempio un nome di dominio, **firmato** (verificato) da terze parti attendibili, ad esempio un' **autorità di certificazione**.  Un certificato fornisce un modo pratico per distribuire chiavi di crittografia pubbliche attendibili. I certificati assicurano quindi che la comunicazione sia attendibile e che invii informazioni crittografate al server appropriato. 

Quando il dispositivo Azure Stack Edge viene configurato inizialmente, i certificati autofirmati vengono generati automaticamente. Facoltativamente, è possibile importare i propri certificati. Se si prevede di usare i propri certificati, è necessario seguire alcune linee guida.

## <a name="types-of-certificates"></a>Tipi di certificati

I vari tipi di certificati usati nel dispositivo Azure Stack Edge sono i seguenti: 
- Certificati di firma
    - CA radice
    - Intermedio

- Certificati endpoint
    - Certificato del nodo
    - Certificati dell'interfaccia utente locale
    - Certificati Azure Resource Manager
    - Certificati di archiviazione BLOB
    - Certificati del dispositivo Internet
    <!--- WiFi certificates
    - VPN certificates-->

- Certificati di crittografia
    - Supportare i certificati di sessione

Ognuno di questi certificati viene descritto in dettaglio nelle sezioni seguenti.

## <a name="signing-chain-certificates"></a>Certificati catena di firma

Si tratta dei certificati per l'autorità che firma i certificati o l'autorità di certificazione di firma. 

### <a name="types"></a>Tipi

Questi certificati possono essere certificati radice o certificati intermedi. I certificati radice sono sempre autofirmati o firmati da solo. I certificati intermedi non sono autofirmati e sono firmati dall'autorità di firma.

### <a name="caveats"></a>Precisazioni

- I certificati radice devono essere certificati della catena di firma.
- I certificati radice possono essere caricati nel dispositivo nel formato seguente: 
    - **Der** : sono disponibili come estensione di `.cer` file.
    - **Base-64 con codifica o PEM** : sono disponibili `.cer` anche come estensione.
    - **P7b** : questo formato viene usato solo per i certificati Chain di firma che includono i certificati radice e intermedi.
- I certificati della catena di firma vengono sempre caricati prima di caricare qualsiasi altro certificato.


## <a name="node-certificates"></a>Certificati del nodo

<!--Your Azure Stack Edge device could be a 1-node device or a 4-node device.--> Tutti i nodi del dispositivo si comunicano costantemente tra loro e pertanto devono avere una relazione di trust. I certificati del nodo forniscono un modo per stabilire la relazione di trust. Quando ci si connette al nodo del dispositivo usando una sessione remota di PowerShell su HTTPS, i certificati del nodo entrano anche in gioco.

### <a name="caveats"></a>Precisazioni

- Il certificato del nodo deve essere specificato in `.pfx` formato con una chiave privata che può essere esportata. 
- È possibile creare e caricare un certificato del nodo con caratteri jolly o 4 singoli certificati del nodo. 
- È necessario modificare un certificato del nodo se il dominio DNS viene modificato, ma il nome del dispositivo non cambia. Se si sta portando il proprio certificato del nodo, non è possibile modificare il numero di serie del dispositivo. è possibile modificare solo il nome di dominio.
- Usare la tabella seguente per istruzioni per la creazione di un certificato del nodo.
   
    |Tipo |Nome soggetto (SN)  |Nome alternativo del soggetto (SAN)  |Esempio di nome soggetto |
    |---------|---------|---------|---------|
    |Nodo|`<NodeSerialNo>.<DnsDomain>`|`*.<DnsDomain>`<br><br>`<NodeSerialNo>.<DnsDomain>`|`mydevice1.microsoftdatabox.com` |
   

## <a name="endpoint-certificates"></a>Certificati endpoint

Per tutti gli endpoint esposti dal dispositivo, è necessario un certificato per le comunicazioni attendibili. I certificati dell'endpoint includono quelli necessari per l'accesso alla Azure Resource Manager e all'archiviazione BLOB tramite le API REST. 

Quando si porta un certificato firmato, è necessaria anche la catena di firma corrispondente del certificato. Per la catena di firma, Azure Resource Manager e i certificati BLOB sul dispositivo, sarà necessario disporre dei certificati corrispondenti nel computer client anche per l'autenticazione e la comunicazione con il dispositivo.

### <a name="caveats"></a>Precisazioni

- I certificati dell'endpoint devono essere nel `.pfx` formato con una chiave privata. La catena di firma deve essere in formato DER ( `.cer` estensione di file). 
- Quando si portano i propri certificati di endpoint, questi possono essere come singoli certificati o certificati multidominio. 
- Se si sta importando una catena di firma, il certificato della catena di firma deve essere caricato prima di caricare un certificato dell'endpoint.
- Questi certificati devono essere modificati se il nome del dispositivo o i nomi di dominio DNS cambiano.
- È possibile utilizzare un certificato di endpoint con caratteri jolly.
- Le proprietà dei certificati dell'endpoint sono simili a quelle di un tipico certificato SSL. 
- Usare la tabella seguente per la creazione di un certificato dell'endpoint:

    |Tipo |Nome soggetto (SN)  |Nome alternativo del soggetto (SAN)  |Esempio di nome soggetto |
    |---------|---------|---------|---------|
    |Azure Resource Manager|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |Archiviazione - BLOB|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |Certificato singolo a più SAN per entrambi gli endpoint|`<Device name>.<dnsdomain>`|`<Device name>.<dnsdomain>`<br>`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |


## <a name="local-ui-certificates"></a>Certificati dell'interfaccia utente locale

È possibile accedere all'interfaccia utente Web locale del dispositivo tramite un browser. Per assicurarsi che la comunicazione sia protetta, è possibile caricare il proprio certificato. 

### <a name="caveats"></a>Precisazioni

- Il certificato dell'interfaccia utente locale viene anche caricato in un `.pfx` formato con una chiave privata che può essere esportata.
- Dopo aver caricato il certificato dell'interfaccia utente locale, sarà necessario riavviare il browser e cancellare la cache. Vedere le istruzioni specifiche per il browser.

    |Tipo |Nome soggetto (SN)  |Nome alternativo del soggetto (SAN)  |Esempio di nome soggetto |
    |---------|---------|---------|---------|
    |Interfaccia utente locale| `<Device name>.<DnsDomain>`|`<Device name>.<DnsDomain>`| `mydevice1.microsoftdatabox.com` |
   

## <a name="iot-edge-device-certificates"></a>Certificati del dispositivo IoT Edge

Il dispositivo Azure Stack Edge è anche un dispositivo Internet delle cose con il calcolo abilitato da un dispositivo IoT Edge connesso. Per le comunicazioni sicure tra questo dispositivo IoT Edge e i dispositivi downstream che possono connettersi ad esso, è anche possibile caricare IoT Edge certificati. 

Il dispositivo dispone di certificati autofirmati che possono essere usati se si vuole usare solo lo scenario di calcolo con il dispositivo. Se il dispositivo Azure Stack Edge è comunque connesso ai dispositivi downstream, sarà necessario importare i propri certificati.

Sono disponibili tre IoT Edge certificati che è necessario installare per abilitare questa relazione di trust:

- **Autorità di certificazione radice o autorità di certificazione proprietario**
- **Autorità di certificazione del dispositivo** 
- **Certificato chiave del dispositivo**

### <a name="caveats"></a>Precisazioni

- I certificati IoT Edge vengono caricati nel `.pem` formato. 


Per ulteriori informazioni sui certificati IoT Edge, vedere [Azure IOT Edge i dettagli del certificato](../iot-edge/iot-edge-certs.md#iot-edge-certificates).

## <a name="support-session-certificates"></a>Supportare i certificati di sessione

Se il dispositivo Azure Stack Edge sta riscontrando problemi, per risolvere questi problemi è possibile che nel dispositivo venga aperta una sessione di supporto di PowerShell remota. Per abilitare una comunicazione crittografata e protetta su questa sessione di supporto, è possibile caricare un certificato.

### <a name="caveats"></a>Precisazioni

- Verificare che il certificato corrispondente `.pfx` con la chiave privata sia installato nel computer client usando lo strumento di decrittografia.
- Verificare che il campo **utilizzo chiavi** per il certificato non sia **firmato**da un certificato. Per verificarlo, fare clic con il pulsante destro del mouse sul certificato, scegliere **Apri** e nella scheda **Dettagli** trovare **utilizzo chiave**. 


### <a name="caveats"></a>Precisazioni

- Il certificato della sessione di supporto deve essere fornito come formato DER con un' `.cer` estensione.


<!--## VPN certificates

If VPN is configured on your Azure Stack Edge device, then you will also need a certificate for any communication that occurs over the VPN channel. You can bring your own VPN certificate to ensure the communication is trusted.

### Caveats

- The VPN certificate must be uploaded as a pfx format with a  private key.
- The VPN certificate is not dependant on the device name, device serial number, or device configuration. It only requires the external FQDN.
- Make sure that the client OID is set.-->

<!--## WiFi certificates

If your device is configured to operate on a wireless network, then you will also need a WiFi certificate for any communication that occurs over the wireless network. 

### Caveats

- The WiFi certificate must be uploaded as a pfx format with a private key.
- Make sure that the client OID is set.-->


## <a name="create-certificates-optional"></a>Creare certificati (facoltativo)

Nella sezione seguente viene descritta la procedura per creare la catena di firma e i certificati dell'endpoint.

### <a name="certificate-workflow"></a>Flusso di lavoro certificato

Sarà possibile creare i certificati per i dispositivi che operano nell'ambiente in uso. È possibile utilizzare i certificati forniti dall'amministratore IT. 

**Solo a scopo di sviluppo o test, è anche possibile usare Windows PowerShell per creare certificati nel sistema locale.** Durante la creazione dei certificati per il client, attenersi alle seguenti linee guida:

1. È possibile creare uno dei seguenti tipi di certificati:

    - Creare un singolo certificato valido per l'uso con un singolo nome di dominio completo (FQDN). Ad esempio, *mydomain.com*.
    - Creare un certificato con caratteri jolly per proteggere anche il nome di dominio principale e più domini secondari. Ad esempio, **. mydomain.com*.
    - Creare un certificato di nome alternativo del soggetto (SAN) che coprirà più nomi di dominio in un singolo certificato. 

2. Se si sta portando un certificato, sarà necessario un certificato radice per la catena di firma. Vedere i passaggi per [creare i certificati della catena di firma](#create-signing-chain-certificate).

3. È quindi possibile creare i certificati dell'endpoint per l'interfaccia utente locale del dispositivo, del BLOB e del Azure Resource Manager. È possibile creare 3 certificati distinti per l'appliance, il BLOB e la Azure Resource Manager oppure è possibile creare un certificato per tutti e tre gli endpoint. Per i passaggi dettagliati, vedere [creare la firma e i certificati dell'endpoint](#create-signed-endpoint-certificates).

4. Indipendentemente dal fatto che si creino 3 certificati distinti o un certificato, specificare i nomi di soggetto (SN) e i nomi alternativi del soggetto (SAN) in base alle indicazioni fornite per ogni tipo di certificato. 

### <a name="create-signing-chain-certificate"></a>Crea certificato catena di firma

Creare questi certificati tramite Windows PowerShell in modalità amministratore. **I certificati creati in questo modo devono essere utilizzati solo a scopo di sviluppo o test.**

Il certificato della catena di firma deve essere creato una sola volta. Gli altri certificati endpoint faranno riferimento a questo certificato per la firma.
 

```azurepowershell
$cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature -Subject "CN=RootCert" -HashAlgorithm sha256 -KeyLength 2048 -CertStoreLocation "Cert:\LocalMachine\My" -KeyUsageProperty Sign -KeyUsage CertSign
```


### <a name="create-signed-endpoint-certificates"></a>Creare certificati dell'endpoint firmato

Creare questi certificati tramite Windows PowerShell in modalità amministratore.

In questi esempi vengono creati certificati per gli endpoint per un dispositivo con:
    - Nome dispositivo: `DBE-HWDC1T2`
    - Dominio DNS: `microsoftdatabox.com`

Sostituire con il nome e il dominio DNS del dispositivo per creare i certificati per il dispositivo.
 
**Certificato dell'endpoint BLOB**

Creare un certificato per l'endpoint BLOB nell'archivio personale.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "*.blob.$AppName.$domain" -Subject "CN=*.blob.$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**Certificato dell'endpoint Azure Resource Manager**

Creare un certificato per gli endpoint Azure Resource Manager nell'archivio personale.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "management.$AppName.$domain","login.$AppName.$domain" -Subject "CN=management.$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**Certificato dell'interfaccia utente Web locale del dispositivo**

Creare un certificato per l'interfaccia utente Web locale del dispositivo nell'archivio personale.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "$AppName.$domain" -Subject "CN=$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**Singolo certificato multisan per tutti gli endpoint**

Creare un singolo certificato per tutti gli endpoint nell'archivio personale.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"
$DeviceSerial = "HWDC1T2"

New-SelfSignedCertificate -Type Custom -DnsName "$AppName.$domain","$DeviceSerial.$domain","management.$AppName.$domain","login.$AppName.$domain","*.blob.$AppName.$domain" -Subject "CN=$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

Una volta creati i certificati, il passaggio successivo consiste nel caricare i certificati sul dispositivo Azure Stack Edge


## <a name="upload-certificates"></a>Carica certificati 

Per impostazione predefinita, i certificati creati per il dispositivo si trovano nell' **archivio personale** del client. Questi certificati devono essere esportati nel client in file di formato appropriati che possono quindi essere caricati nel dispositivo.

1. Il certificato radice deve essere esportato come formato DER con `.cer` estensione. Per i passaggi dettagliati, vedere [esportare i certificati come formato der](#export-certificates-as-der-format).
2. I certificati dell'endpoint devono essere esportati come file con *estensione pfx* con chiavi private. Per i passaggi dettagliati, vedere [esportare i certificati come file con *estensione pfx* con chiavi private](#export-certificates-as-pfx-format-with-private-key). 
3. I certificati radice e dell'endpoint vengono quindi caricati sul dispositivo usando l'opzione **+ Aggiungi certificato** nella pagina certificati dell'interfaccia utente Web locale. 

    1. Caricare prima i certificati radice. Nell'interfaccia utente Web locale passare a **certificati > + Aggiungi certificato**.

        ![Aggiungi certificato della catena di firma](media/azure-stack-edge-series-manage-certificates/add-cert-1.png)

    2. Caricare quindi i certificati dell'endpoint. 

        ![Aggiungi certificato della catena di firma](media/azure-stack-edge-series-manage-certificates/add-cert-2.png)

        Scegliere i file di certificato in formato *PFX* e immettere la password specificata al momento dell'esportazione del certificato. L'applicazione del certificato Azure Resource Manager potrebbe richiedere alcuni minuti.

        Se la catena di firma non viene aggiornata per prima e si tenta di caricare i certificati dell'endpoint, si riceverà un errore.

        ![Errore Apply certificate](media/azure-stack-edge-series-manage-certificates/apply-cert-error-1.png)

        Tornare indietro e caricare il certificato della catena di firma e quindi caricare e applicare i certificati dell'endpoint.

> [!IMPORTANT]
> Se il nome del dispositivo o il dominio DNS sono stati modificati, è necessario creare nuovi certificati. I certificati client e i certificati del dispositivo devono essere aggiornati con il nuovo nome del dispositivo e il dominio DNS. 

## <a name="import-certificates-on-the-client-accessing-the-device"></a>Importare i certificati sul client che accedono al dispositivo

I certificati creati e caricati nel dispositivo devono essere importati nel client Windows (che accede al dispositivo) nell'archivio certificati appropriato.

1. Il certificato radice esportato come DER verrà ora importato nelle **autorità di certificazione radice attendibili** nel sistema client. Per i passaggi dettagliati, vedere [importare certificati nell'archivio Autorità di certificazione radice attendibili](#import-certificates-as-der-format).

2. I certificati di endpoint esportati come `.pfx` devono essere esportati come der con `.cer` estensione. Questa operazione `.cer` viene quindi importata nell' **archivio certificati personali** del sistema. Per i passaggi dettagliati, vedere [importare certificati nell'archivio certificati personale](#import-certificates-as-der-format).

### <a name="import-certificates-as-der-format"></a>Importa certificati come formato DER

Per importare i certificati in un client Windows, eseguire le operazioni seguenti:

1. Fare clic con il pulsante destro del mouse sul file e scegliere **Installa certificato**. Questa azione avvia l'Importazione guidata certificati.

    ![Importare il certificato 1](media/azure-stack-edge-series-manage-certificates/import-cert-1.png)

2. In **percorso archivio**selezionare **computer locale**, quindi fare clic su **Avanti**.

    ![Importare il certificato 2](media/azure-stack-edge-series-manage-certificates/import-cert-2.png)

3. Selezionare **colloca tutti i certificati nel seguente archivio**e quindi selezionare **Sfoglia**. 

    - Per importare nell'archivio personale, passare all'archivio personale dell'host remoto, quindi fare clic su **Avanti**.

        ![Importa certificato 4](media/azure-stack-edge-series-manage-certificates/import-cert-4.png)


    - Per importare nell'archivio attendibile, passare all'autorità di certificazione radice attendibile, quindi selezionare **Avanti**.

        ![Importa certificato 3](media/azure-stack-edge-series-manage-certificates/import-cert-3.png)

 
4. Selezionare **Fine**. Viene visualizzato un messaggio relativo all'esito positivo dell'importazione.

### <a name="export-certificates-as-pfx-format-with-private-key"></a>Esportare i certificati come formato pfx con la chiave privata

Per esportare un certificato SSL con chiave privata in un computer Windows, seguire questa procedura. 

> [!IMPORTANT]
> Eseguire questi passaggi nello stesso computer utilizzato per creare il certificato. 

1. Eseguire *certlm. msc* per avviare l'archivio certificati del computer locale.

1. Fare doppio clic sulla cartella **Personal** , quindi su **Certificates**.

    ![Esporta certificato 1](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-1.png)
 
2. Fare clic con il pulsante destro del mouse sul certificato di cui si desidera eseguire il backup e scegliere **tutte le attività > Esporta...**

    ![Esporta certificato 2](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-2.png)

3. Seguire l'esportazione guidata certificati per eseguire il backup del certificato in un file con estensione pfx.

    ![Esporta certificato 3](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-3.png)

4. Scegliere **Sì, Esporta la chiave privata**.

    ![Esporta certificato 4](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-4.png)

5. Scegliere **Includi tutti i certificati nel percorso del certificato, se possibile**, **esportare tutte le proprietà estese** e **abilitare la privacy del certificato**. 

    > [!IMPORTANT]
    > Non selezionare l' **opzione Elimina chiave privata se l'esportazione ha esito positivo**.

    ![Esporta certificato 5](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-5.png)

6. Immettere una password da ricordare. Confermare la password. La password protegge la chiave privata.

    ![Esporta certificato 6](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-6.png)

7. Scegliere di salvare il file in un percorso set.

    ![Esporta certificato 7](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-7.png)
  
8. Selezionare **Fine**.

    ![Esporta certificato 8](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-8.png)

9. Viene visualizzato un messaggio in cui l'esportazione ha avuto esito positivo. Selezionare **OK**.

    ![Esporta certificato 9](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-9.png)

Il backup del file con estensione pfx è ora salvato nel percorso selezionato ed è pronto per essere spostato o archiviato per garantire la sicurezza.


### <a name="export-certificates-as-der-format"></a>Esporta certificati come formato DER

1. Eseguire *certlm. msc* per avviare l'archivio certificati del computer locale.

1. Nell'archivio certificati personali selezionare il certificato radice. Fare clic con il pulsante destro del mouse e scegliere **tutte le attività > Esporta...**

    ![Esporta certificato 1](media/azure-stack-edge-series-manage-certificates/export-cert-cer-1.png)

2. Verrà avviata la procedura guidata del certificato. Selezionare il formato come **file binario codificato der X. 509 (. cer)**. Selezionare **Avanti**.

    ![Esporta certificato 2](media/azure-stack-edge-series-manage-certificates/export-cert-cer-2.png)

3. Individuare e selezionare la posizione in cui si desidera esportare il file di formato. cer.

    ![Esporta certificato 3](media/azure-stack-edge-series-manage-certificates/export-cert-cer-3.png)


4. Selezionare **Fine**.

    ![Esporta certificato 4](media/azure-stack-edge-series-manage-certificates/export-cert-cer-4.png)


## <a name="supported-certificate-algorithms"></a>Algoritmi di certificato supportati

 Con il dispositivo Azure Stack Edge sono supportati solo i certificati RSA (Rivest-Shamir-Adleman). Se vengono usati i certificati ECDSA (ellittica Digital Signature Algorithm), il comportamento del dispositivo è indeterminato.

 I certificati che contengono una chiave pubblica RSA sono detti certificati RSA. I certificati che contengono una chiave pubblica di crittografia a curva ellittica (ECC) vengono definiti certificati ECDSA (algoritmo di firma digitale a curva ellittica). 


## <a name="view-certificate-expiry"></a>Visualizza scadenza certificato

Se si importano i propri certificati, i certificati scadranno in genere tra 1 anno o 6 mesi. Per visualizzare la data di scadenza del certificato, passare alla pagina **certificati** nell'interfaccia utente Web locale del dispositivo. Se si seleziona un certificato specifico, è possibile visualizzare la data di scadenza del certificato.

## <a name="rotate-certificates"></a>Ruotare i certificati

La rotazione dei certificati non è implementata in questa versione. Non viene inoltre ricevuta alcuna notifica della data di scadenza in sospeso per il certificato. 

Visualizzare la data di scadenza del certificato nella pagina **certificati** nell'interfaccia utente Web locale del dispositivo. Una volta raggiunta la scadenza del certificato, creare e caricare nuovi certificati in base alle istruzioni dettagliate riportate in [creare e caricare certificati](azure-stack-edge-j-series-manage-certificates.md).

## <a name="next-steps"></a>Passaggi successivi

[Distribuire il dispositivo Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md)
