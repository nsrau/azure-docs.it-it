---
title: Creare certificati autofirmati per le connessioni cross-premise di una rete virtuale da punto a sito usando makecert | Documentazione Microsoft
description: Questo articolo contiene i passaggi per usare makecert e creare certificati autofirmati in Windows 10.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 27b99f7c-50dc-4f88-8a6e-d60080819a43
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/19/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 64c6e8fa5288ce9a25bcb434217511425aebfe21
ms.openlocfilehash: beb873c99d956eafebecc69f9afc480c558efabb


---
# <a name="working-with-self-signed-certificates-for-point-to-site-connections"></a>Usare i certificati autofirmati per le connessioni da punto a sito
Questo articolo spiega come creare un certificato autofirmato tramite **makecert** e come usarlo per generare certificati client. Per le connessioni da punto a sito, il metodo preferito è l'uso della soluzione aziendale per la creazione di certificati. Assicurarsi di generare i certificati client nel formato del valore di nome comune 'name@yourdomain.com',, anziché nel formato NetBIOS "nome di dominio\nomeutente".

Se non si dispone di una soluzione enterprise, è richiesto un certificato autofirmato per consentire ai client da punto a sito per connettersi a una rete virtuale. Anche se è possibile usare PowerShell per creare certificati, il certificato generato con PowerShell non contiene i campi necessari ad Azure per l'autenticazione da punto a sito. Makecert è uno strumento convalidato per creare i certificati compatibili con le connessioni da punto a sito Makecert non è stato deprecato per l'uso con le configurazioni da punto a sito.

## <a name="create-a-self-signed-certificate"></a>Creare un certificato autofirmato
La seguente procedura offre una guida completa alla creazione di un certificato autofirmato tramite makecert. Questi passaggi non sono specifici di un modello di distribuzione. Sono validi sia per Gestione risorse che per il modello classico.

### <a name="to-create-a-self-signed-certificate"></a>Per creare un certificato autofirmato
1. Da un computer che esegue Windows 10, scaricare e installare [Windows Software Development Kit (SDK) per Windows 10](https://dev.windows.com/en-us/downloads/windows-10-sdk).
2. Dopo l'installazione, è possibile trovare l'utilità makecert.exe nel percorso seguente: C:\Programmi (x86)\Windows Kits\10\bin\<arch>. 
   
    Esempio: `C:\Program Files (x86)\Windows Kits\10\bin\x64`
3. Creare e installare quindi un certificato nell'archivio Certificati personali nel computer in uso. Nell'esempio seguente viene creato un file *.cer* corrispondente da caricare in Azure quando si configura una connessione da punto a sito. Eseguire il comando seguente come amministratore. Sostituire *ARMP2SRootCert* e *ARMP2SRootCert.cer* con il nome da assegnare al certificato.<br><br>Il certificato si troverà in Certificati -Utente corrente\Personale\Certificati.
   
        makecert -sky exchange -r -n "CN=ARMP2SRootCert" -pe -a sha1 -len 2048 -ss My "ARMP2SRootCert.cer"

### <a name="a-namerootpublickeyato-obtain-the-public-key"></a><a name="rootpublickey"></a>Per ottenere la chiave pubblica
Durante la configurazione del gateway VPN per le connessioni da punto a sito, in Azure viene caricata la chiave pubblica relativa al certificato radice.

1. Per ottenere un file con estensione .cer dal certificato, aprire **certmgr.msc**. Fare clic con il pulsante destro del mouse sul certificato radice autofirmato, scegliere **Tutte le attività** e quindi fare clic su **Esporta**. Si avvia la procedura di **Esportazione guidata certificati**.
2. Nella procedura guidata fare clic su **Avanti**, selezionare **No, non esportare la chiave privata** e quindi fare clic su **Avanti**.
3. Nella pagina **Formato file di esportazione** selezionare **Codificato Base&64; X.509 (.CER)**. Quindi fare clic su **Avanti**. 
4. In **File da esportare** fare clic su **Sfoglia** e passare alla posizione in cui si vuole esportare il certificato. Per **Nome file**, assegnare un nome al file del certificato. Quindi fare clic su **Avanti**.
5. Fare clic su **Fine** per esportare il certificato.

### <a name="export-the-self-signed-certificate-optional"></a>Esportare il un certificato autofirmato (facoltativo)
Si consiglia di esportare il certificato autofirmato e archiviarlo in un percorso sicuro. Se necessario, in seguito è possibile installarlo su un altro computer e generare altri certificati client oppure esportare un altro file.cer. Eventuali computer con un certificato client installato e configurati con impostazioni del client VPN corrette possono connettersi alla rete virtuale tramite P2S. Per questo motivo, è opportuno assicurarsi che i certificati client vengano generati e installati solo quando richiesto e che il certificato autofirmato sia archiviato in modo sicuro.

Per esportare il certificato autofirmato come file .pfx, selezionare il certificato radice ed eseguire la stessa procedura finalizzata a [esportare un certificato client](#clientkey) .

## <a name="create-and-install-client-certificates"></a>Creare e installare i certificati client
Il certificato autofirmato non deve essere installato direttamente nel computer client. È necessario generare un certificato client da un certificato autofirmato. Quindi, esportare e installare il certificato client nel computer client. I seguenti passaggi non si applicano specificatamente a un modello di distribuzione. Sono validi sia per Gestione risorse che per il modello classico.

### <a name="part-1---generate-a-client-certificate-from-a-self-signed-certificate"></a>Parte 1: generare un certificato client da un certificato autofirmato
I passaggi seguenti illustrano come generare un certificato client da un certificato autofirmato. È possibile generare più certificati client dallo stesso certificato. Ogni certificato client può essere esportato e installato nel computer client. 

1. Nello stesso computer usato per creare il certificato autofirmato aprire un prompt dei comandi come amministratore.
2. In questo esempio "ARMP2SRootCert" fa riferimento al certificato autofirmato generato. 
   
   * Modificare *"ARMP2SRootCert"* con il nome della radice autofirmata dalla quale si intende generare il certificato client. 
   * Modificare *ClientCertificateName* con il nome che si desidera assegnare al certificato client generato. 

    Modificare ed eseguire l'esempio per generare un certificato client. Se si esegue l'esempio riportato di seguito senza modificarlo, si otterrà un certificato client denominato ClientCertificateName nell'archivio dei certificati personale generato dal certificato radice ARMP2SRootCert.

        makecert.exe -n "CN=ClientCertificateName" -pe -sky exchange -m 96 -ss My -in "ARMP2SRootCert" -is my -a sha1

1. Tutti i certificati vengono archiviati nell'archivio Certificati - Utente corrente\Personale\Certificati nel computer in uso. È possibile generare tutti i certificati client necessari in base a questa procedura.

### <a name="a-nameclientkeyapart-2---export-a-client-certificate"></a><a name="clientkey"></a>Parte 2: esportare un certificato client
1. Per esportare un certificato client, aprire **certmgr.msc**. Fare clic con il pulsante destro del mouse sul certificato client da esportare, scegliere **Tutte le attività** e quindi fare clic su **Esporta**. Si avvia la procedura di **Esportazione guidata certificati**.
2. Nella procedura guidata, fare clic su **Avanti**, selezionare **Sì, esporta la chiave privata** e quindi fare clic su **Avanti**.
3. Nella pagina **Formato file di esportazione** è possibile lasciare selezionate le impostazioni predefinite. Quindi fare clic su **Avanti**. 
4. Nella pagina **Sicurezza** è necessario proteggere la chiave privata. Se si sceglie di usare una password, assicurarsi di registrare o ricordare quella impostata per questo certificato. Quindi fare clic su **Avanti**.
5. In **File da esportare** fare clic su **Sfoglia** e passare alla posizione in cui si vuole esportare il certificato. Per **Nome file**, assegnare un nome al file del certificato. Quindi fare clic su **Avanti**.
6. Fare clic su **Fine** per esportare il certificato.    

### <a name="part-3---install-a-client-certificate"></a>Parte 3: installare un certificato client
In ogni client che si desidera connettere alla rete virtuale con una connessione da punto a sito deve essere stato installato un certificato client. Questo certificato è aggiuntivo rispetto al pacchetto di configurazione VPN richiesto. La procedura seguente illustra come installare manualmente il certificato client.

1. Individuare e copiare il file *.pfx* nel computer client. Nel computer client, fare doppio clic sul file *.pfx* per installarlo. Lasciare il **Percorso archivio** impostato come **Utente corrente** e quindi fare clic su **Avanti**.
2. Nella pagina **File da importare** non apportare alcuna modifica. Fare clic su **Avanti**.
3. Nella pagina **Protezione della chiave privata** immettere la password per il certificato, se usato, o verificare che l'entità di sicurezza che installa il certificato sia corretta, quindi fare clic su **Avanti**.
4. Nella pagina **Archivio certificati** lasciare la posizione come predefinita e quindi fare clic su **Avanti**.
5. Fare clic su **Fine**. In **Avviso di sicurezza** per l'installazione del certificato fare clic su **Sì**. È possibile fare clic su "Sì" perché il certificato è stato generato. Il certificato è stato importato correttamente.

## <a name="next-steps"></a>Passaggi successivi
Continuare con la configurazione da punto a sito. 

* Per i passaggi del modello di distribuzione di **Resource Manager** , vedere l'articolo relativo a come [configurare una connessione da punto a sito a una rete VNet con PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md). 
* Per i passaggi del modello di distribuzione **classico** , vedere [Configurare una connessione VPN da punto a sito a una rete virtuale tramite il portale classico](vpn-gateway-point-to-site-create.md).




<!--HONumber=Jan17_HO3-->


