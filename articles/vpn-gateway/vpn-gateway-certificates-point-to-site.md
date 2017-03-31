---
title: 'Creare certificati autofirmati per connessioni da punto a sito: PowerShell: Azure | Microsoft Docs'
description: Questo articolo contiene i passaggi per creare un certificato radice autofirmato, esportare la chiave pubblica e generare certificati client con PowerShell in Windows 10.
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
ms.date: 03/20/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 20fb95341d6240b883e711cf771c33f6d8978cb9
ms.lasthandoff: 03/22/2017


---
# <a name="create-a-self-signed-root-certificate-for-point-to-site-connections-using-powershell"></a>Creare un certificato radice autofirmato per connessioni da punto a sito con PowerShell

Le connessioni da punto a sito usano certificati per l'autenticazione. Quando si configura una connessione da punto a sito è necessario caricare la chiave pubblica (file con estensione .cer) di un certificato radice in Azure. Questo articolo spiega come creare un certificato radice autofirmato, esportare la chiave pubblica e generare e installare i certificati client.

> [!NOTE]
> In precedenza makecert era il metodo consigliato per creare certificati radice autofirmati e generare i certificati client per le connessioni da punto a sito. Ora è possibile usare PowerShell per creare questi certificati. Un vantaggio dell'uso di PowerShell è costituito dalla possibilità di creare certificati SHA-2. 
>
>

## <a name="rootcert"></a>Creare un certificato radice autofirmato

La seguente procedura spiega come creare un certificato radice autofirmato tramite PowerShell. Per completare la procedura è necessario Windows 10. I cmdlet e i parametri utilizzati in questi passaggi fanno parte del sistema operativo Windows 10 e non di una versione di PowerShell.

1. Da un computer che esegue Windows 10 aprire una console di Windows PowerShell con privilegi elevati.
2. Usare l'esempio seguente per creare il certificato radice autofirmato. L'esempio seguente crea un certificato radice autofirmato denominato "P2SRootCert" che viene installato automaticamente in "Certificati-Utente corrente\Personale\Certificati". È possibile visualizzare il certificato aprendo *certmgr.msc*.

        $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
        -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
        -HashAlgorithm sha256 -KeyLength 2048 `
        -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign

### <a name="cer"></a>Per ottenere la chiave pubblica

Le connessioni da punto a sito richiedono il caricamento della chiave pubblica (file con estensione .cer) in Azure. La procedura seguente consente di esportare il file con estensione .cer per il certificato radice autofirmato.

1. Per ottenere un file con estensione .cer dal certificato, aprire **certmgr.msc**. Individuare il certificato radice autofirmato, in genere in "Certificati - Utente corrente\Personale\Certificati" e fare clic con il pulsante destro del mouse. Fare clic su **Tutte le attività** e quindi su **Esporta**. Si avvia la procedura di **Esportazione guidata certificati**.
2. Nella procedura guidata fare clic su **Avanti**. Selezionare **No, non esportare la chiave privata** e quindi fare clic su **Avanti**.
3. Nella pagina **Formato file di esportazione** selezionare **Codificato Base 64 X.509 (.CER)** e quindi fare clic su **Avanti**. 
4. In **File da esportare** fare clic su **Sfoglia** e passare alla posizione in cui si vuole esportare il certificato. Per **Nome file**, assegnare un nome al file del certificato. Quindi fare clic su **Avanti**.
5. Fare clic su **Fine** per esportare il certificato. Verrà visualizzato il messaggio **Esportazione completata**. Fare clic su **OK** per chiudere la procedura guidata.

### <a name="to-export-a-self-signed-root-certificate-optional"></a>Per esportare un certificato radice autofirmato (opzionale)
Si consiglia di esportare il certificato radice autofirmato e archiviarlo in un percorso sicuro. Se necessario, in seguito è possibile installarlo su un altro computer e generare altri certificati client oppure esportare un altro file.cer.

Per esportare il certificato radice autofirmato come file .pfx, selezionare il certificato radice ed eseguire la stessa procedura che si usa per [esportare un certificato client](#clientexport).

## <a name="clientcert"></a>Generazione di un certificato client

Ogni computer client che si connette a una rete virtuale usando la soluzione Da punto a sito deve avere un certificato client installato. È possibile generare un certificato client da un certificato radice autofirmato, quindi esportare e installare il certificato client. Se il certificato client non è installato, l'autenticazione ha esito negativo. 

I passaggi seguenti illustrano come generare un certificato client da un certificato radice autofirmato. È possibile generare più certificati client dallo stesso certificato radice. Quando si generano certificati client con la procedura seguente, il certificato client viene installato automaticamente nel computer che è stato usato per generare il certificato. Se si vuole installare un certificato client in un altro computer client, è possibile esportare il certificato.

Per completare la procedura è necessario Windows 10. I cmdlet e i parametri utilizzati in questi passaggi fanno parte del sistema operativo Windows 10 e non di una versione di PowerShell.

### <a name="example-1"></a>Esempio 1

Questo esempio usa la variabile dichiarata "$cert" della sezione precedente. Se è stata chiusa la console di PowerShell dopo aver creato il certificato radice autofirmato o si stanno creando i certificati client aggiuntivi in una nuova sessione della console di PowerShell, attenersi alla procedura nell'esempio 2.

Modificare ed eseguire l'esempio per generare un certificato client. Se si esegue l'esempio seguente senza modificarlo, il risultato è un certificato client denominato "P2SChildCert".  Se si desidera assegnare un nome diverso al certificato figlio, modificare il valore CN. Non modificare il TextExtension quando si esegue questo esempio. Il certificato client generato viene installato automaticamente in "Certificati-Utente corrente\Personale\Certificati" nel computer in uso.

    New-SelfSignedCertificate -Type Custom -KeySpec Signature `
    -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
    -HashAlgorithm sha256 -KeyLength 2048 `
    -CertStoreLocation "Cert:\CurrentUser\My" `
    -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")

### <a name="example-2"></a>Esempio 2

Se si creano certificati client aggiuntivi o non si usa la stessa sessione di PowerShell utilizzata per creare il certificato radice autofirmato, usare la procedura seguente:

1. Identificare il certificato radice autofirmato che viene installato nel computer. Questo cmdlet restituisce un elenco dei certificati installati nel computer in uso.

        Get-ChildItem -Path “Cert:\CurrentUser\My”

2. Individuare il nome dell'oggetto nell'elenco restituito e quindi copiare in un file di testo l'identificazione personale che si trova accanto a esso. Nell'esempio seguente ci sono due certificati. Il nome CN è il nome del certificato radice autofirmato da cui si desidera generare un certificato figlio. In questo caso "P2SRootCert".

        Thumbprint                                Subject

        AED812AD883826FF76B4D1D5A77B3C08EFA79F3F  CN=P2SChildCert4
        7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655  CN=P2SRootCert


3. Dichiarare una variabile per il certificato radice usando l'identificazione personale del passaggio precedente. Sostituire THUMBPRINT con l'identificazione personale del certificato radice autofirmato da cui si desidera generare un certificato figlio.

        $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\THUMBPRINT"

    Ad esempio, usando l'identificazione personale per P2SRootCert nel passaggio precedente, la variabile sarebbe simile alla seguente:

        $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655"
        

4.  Modificare ed eseguire l'esempio per generare un certificato client. Se si esegue l'esempio seguente senza modificarlo, il risultato è un certificato client denominato "P2SChildCert". Se si desidera assegnare un nome diverso al certificato figlio, modificare il valore CN. Non modificare il TextExtension quando si esegue questo esempio. Il certificato client generato viene installato automaticamente in "Certificati-Utente corrente\Personale\Certificati" nel computer in uso.

        New-SelfSignedCertificate -Type Custom -KeySpec Signature `
        -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
        -HashAlgorithm sha256 -KeyLength 2048 `
        -CertStoreLocation "Cert:\CurrentUser\My" `
        -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")

## <a name="clientexport"></a>Esportare un certificato client   

Quando viene generato un certificato client, viene automaticamente installato nel computer che è stato usato per generarlo. Se si vuole installare un certificato client in un altro computer client, è necessario esportare il certificato client generato.                              

1. Per esportare un certificato client, aprire **certmgr.msc**. Per impostazione predefinita, i certificati client generati si trovano in "Certificati-Utente corrente\Personale\Certificati". Fare clic con il pulsante destro del mouse sul certificato client da esportare, scegliere **Tutte le attività** e quindi fare clic su **Esporta**. Si avvia la procedura di **Esportazione guidata certificati**.
2. Nella procedura guidata, fare clic su **Avanti**, selezionare **Sì, esporta la chiave privata** e quindi fare clic su **Avanti**.
3. Nella pagina **Formato file di esportazione** lasciare selezionate le impostazioni predefinite. Verificare che l'opzione "Se possibile, includi tutti i certificati nel percorso certificazione" sia selezionata. Quindi fare clic su **Avanti**.
4. Nella pagina **Sicurezza** è necessario proteggere la chiave privata. Se si sceglie di usare una password, assicurarsi di registrare o ricordare quella impostata per questo certificato. Quindi fare clic su **Avanti**.
5. In **File da esportare** fare clic su **Sfoglia** e passare alla posizione in cui si vuole esportare il certificato. Per **Nome file**, assegnare un nome al file del certificato. Quindi fare clic su **Avanti**.
6. Fare clic su **Fine** per esportare il certificato.    

## <a name="install"></a>Installare un certificato client esportato

Se si vuole creare una connessione da punto a sito da un computer client diverso da quello usato per generare i certificati client, è necessario installare un certificato client. Quando si installa un certificato client, sarà necessaria la password creata quando il certificato client è stato esportato.

1. Individuare e copiare il file *.pfx* nel computer client. Nel computer client, fare doppio clic sul file *.pfx* per installarlo. Lasciare il **Percorso archivio** impostato come **Utente corrente** e quindi fare clic su **Avanti**.
2. Nella pagina **File da importare** non apportare alcuna modifica. Fare clic su **Avanti**.
3. Nella pagina **Protezione della chiave privata** immettere la password per il certificato, se usato, o verificare che l'entità di sicurezza che installa il certificato sia corretta, quindi fare clic su **Avanti**.
4. Nella pagina **Archivio certificati** lasciare la posizione come predefinita e quindi fare clic su **Avanti**.
5. Fare clic su **Fine**. In **Avviso di sicurezza** per l'installazione del certificato fare clic su **Sì**. È possibile fare clic su "Sì" perché il certificato è stato generato. Il certificato è stato importato correttamente.

## <a name="next-steps"></a>Passaggi successivi
Continuare con la configurazione da punto a sito. 

* Per i passaggi del modello di distribuzione di **Resource Manager**, vedere l'articolo relativo a come [configurare una connessione da punto a sito a una rete VNet](vpn-gateway-howto-point-to-site-resource-manager-portal.md). 
* Per i passaggi del modello di distribuzione **classico**, vedere [Configurare una connessione VPN da punto a sito a una rete virtuale (classico)](vpn-gateway-howto-point-to-site-classic-azure-portal.md).


