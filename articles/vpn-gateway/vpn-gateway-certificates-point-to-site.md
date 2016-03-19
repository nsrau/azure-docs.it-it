<properties 
   pageTitle="Creare certificati autofirmati per configurazioni cross-premise di gateway VPN da punto a sito con makecert | Microsoft Azure"
   description="Questo articolo contiene i passaggi per usare makecert e creare certificati radice autofirmati in Windows 10."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/15/2016"
   ms.author="cherylmc" />

# Usare i certificati radice autofirmati per le configurazioni da punto a sito

Le attività riportate di seguito consentono di creare un certificato radice tramite makecert e generare i certificati client dal certificato radice. I passaggi riportati di seguito illustrano l'uso di makecert su Windows 10.

## Creare un certificato radice autofirmato

Makecert è un modo per creare un certificato radice autofirmato. I passaggi riportati di seguito guidano passo per passo alla creazione di un certificato radice autofirmato tramite makecert. Questi passaggi non sono specifici di un modello di distribuzione. Sono validi sia per Gestione risorse che per il modello classico.

### Per creare un certificato radice autofirmato

1. Da un computer che esegue Windows 10, scaricare e installare [Windows Software Development Kit (SDK) per Windows 10](https://dev.windows.com/it-IT/downloads/windows-10-sdk).

2. Dopo l'installazione, è possibile trovare l'utilità makecert.exe in questo percorso: C:\\Programmi (x86) \\Windows Kits\\10\\bin < arch >.
		
	Esempio:
	
		C:\Program Files (x86)\Windows Kits\10\bin\x64\makecert.exe

3. Creare e installare quindi un certificato radice nell'archivio Certificati personali nel computer in uso. L'esempio seguente crea un file *.cer* corrispondente da caricare in un secondo momento. Eseguire il comando seguente come amministratore, dove *RootCertificateName* è il nome che si desidera usare per il certificato.

	Nota: se si esegue l'esempio riportato di seguito senza alcuna modifica, il risultato sarà un certificato radice e il file corrispondente *RootCertificateName.cer*. È possibile trovare il file con estensione cer nella directory da cui è stato eseguito il comando. Il certificato si troverà in Certificati -Utente corrente\\Personale\\Certificati.

    	makecert -sky exchange -r -n "CN=RootCertificateName" -pe -a sha1 -len 2048 -ss My "RootCertificateName.cer"

	>[AZURE.NOTE]Poiché è stato creato un certificato radice da cui verranno generati i certificati client, è possibile esportarlo, insieme alla relativa chiave privata e salvarlo in una posizione sicura dove può essere recuperato.

## Generare, esportare e installare i certificati client

È possibile generare un certificato client da un certificato radice autofirmato, quindi esportare e installare il certificato client. I passaggi riportati sotto non sono specifici di un modello di distribuzione. Sono validi sia per Gestione risorse che per il modello classico.

### Per generare un certificato client da un certificato radice autofirmato.

I passaggi seguenti illustrano come generare un certificato client da un certificato radice autofirmato. È possibile generare più certificati client dallo stesso certificato radice. Ogni certificato client può essere esportato e installato nel computer client.

1. Nello stesso computer usato per creare il certificato radice autofirmato aprire un prompt dei comandi come amministratore.

2. Passare alla directory in cui si vuole salvare il file del certificato client. *RootCertificateName* fa riferimento al certificato radice autofirmato generato. Se si esegue l'esempio riportato di seguito (sostituendo RootCertificateName con il nome del certificato radice), il risultato sarà un certificato client denominato "ClientCertificateName" nell'archivio certificati personale.

3. Digitare il seguente comando:

    	makecert.exe -n "CN=ClientCertificateName" -pe -sky exchange -m 96 -ss My -in "RootCertificateName" -is my -a sha1

4. Tutti i certificati vengono archiviati nell'archivio Certificati - Utente corrente\\Personale\\Certificati nel computer in uso. È possibile generare tutti i certificati client necessari in base a questa procedura.

### Per esportare un certificato client

1. Per esportare un certificato client, usare *certmgr.msc*. Fare clic con il pulsante destro del mouse sul certificato client da esportare, scegliere **Tutte le attività** e quindi fare clic su **Esporta**. Verrà aperta l'Esportazione guidata certificati.

2. Nella procedura guidata, fare clic su **Avanti**, selezionare **Sì, esporta la chiave privata**, quindi fare clic su **Avanti**.

3. Nella pagina **Formato file di esportazione**, è possibile lasciare selezionate le impostazioni predefinite. Quindi fare clic su **Avanti**.
 
4. Nella pagina **Sicurezza**, è necessario proteggere la chiave privata. Se si sceglie di usare una password, assicurarsi di registrare o ricordare quella impostata per questo certificato, quindi fare clic su **Avanti**.

5. In **File da esportare**, fare clic su**Sfoglia** e passare alla posizione in cui si desidera esportare il certificato. Per **Nome file**, assegnare un nome al file del certificato. Quindi fare clic su **Avanti**.

6. Fare clic su **Fine** per esportare il certificato.

### Installare un certificato client

In ogni client che si desidera connettere alla rete virtuale con una connessione da punto a sito deve essere stato installato un certificato client. Questo certificato è aggiuntivo rispetto al pacchetto di configurazione VPN richiesto. La procedura seguente illustra l'installazione manuale del certificato client.

1. Individuare e copiare il file *.pfx* nel computer client. Nel computer client, fare doppio clic sul file *.pfx* per installarlo. Lasciare il **Percorso archivio** impostato come **Utente corrente**, quindi fare clic su **Avanti**.

2. Nella pagina **File da importare**, non apportare alcuna modifica. Fare clic su **Avanti**.

3. Nella pagina **Protezione della chiave privata**, immettere la password per il certificato se impostata o verificare che l'entità di sicurezza che installa il certificato sia corretta, quindi fare clic su **Avanti**.

4. Nella pagina **Archivio certificati**, lasciare la posizione come predefinita, quindi fare clic su **Avanti**.

5. Fare clic su **Finish**. In **Avviso di sicurezza** per l'installazione del certificato, fare clic su **Sì**. Il certificato è stato importato correttamente.

## Passaggi successivi

Continuare con la configurazione da punto a sito.

- Per i passaggi del modello di distribuzione **Gestione risorse**, vedere l'articolo relativo a come [configurare una connessione da punto a sito a una rete virtuale con PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md). 
- Per i passaggi del modello di distribuzione **classico**, vedere [Configurare una connessione VPN da punto a sito a una rete virtuale](vpn-gateway-point-to-site-create.md).

<!---HONumber=AcomDC_0121_2016-->