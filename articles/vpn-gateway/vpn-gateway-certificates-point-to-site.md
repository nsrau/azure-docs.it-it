<properties 
   pageTitle="Creare certificati autofirmati per le connessioni cross-premise di una rete virtuale Point-to-Site utilizzando makecert | Microsoft Azure"
   description="Questo articolo contiene i passaggi per usare makecert e creare certificati autofirmati in Windows 10."
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
   ms.date="08/15/2016"
   ms.author="cherylmc" />

# Usare i certificati autofirmati per le connessioni Point-to-Site

Questo articolo spiega come creare un certificato autofirmato tramite makecert, da cui generare i certificati client. I passaggi illustrano l'uso di makecert su Windows 10. Makecert è uno strumento convalidato per creare i certificati compatibili con le connessioni Point-to-Site.

Per le connessioni Point-to-Site, il metodo preferito per i certificati consiste nell'utilizzare la soluzione di classe enterprise, assicurandosi di emettere i certificati client nel formato del valore nome comune 'name@yourdomain.com', anziché nel formato 'NetBIOS domain name\\username' (cognome\\nome dominio NetBIOS).

Se non si dispone di una soluzione enterprise, è richiesto un certificato autofirmato per consentire ai client Point-to-Style per connettersi a una rete virtuale. Siamo consapevoli che makecert è stato deprecato, ma è ancora un metodo valido per creare certificati autofirmati compatibili con le connessioni Point-to-Site.

## Creare un certificato autofirmato

Makecert è un modo per creare un certificato autofirmato. La seguente procedura offre una guida completa alla creazione di un certificato autofirmato tramite makecert. Questi passaggi non sono specifici di un modello di distribuzione. Sono validi sia per Gestione risorse che per il modello classico.

### Per creare un certificato autofirmato

1. Da un computer che esegue Windows 10, scaricare e installare [Windows Software Development Kit (SDK) per Windows 10](https://dev.windows.com/it-IT/downloads/windows-10-sdk).

2. Dopo l'installazione, è possibile trovare l'utilità makecert.exe in questo percorso: C:\\Programmi (x86) \\Windows Kits\\10\\bin < arch >.
		
	Esempio:
	
		C:\Program Files (x86)\Windows Kits\10\bin\x64\makecert.exe

3. Creare e installare quindi un certificato nell'archivio Certificati personali nel computer in uso. Nell'esempio seguente viene creato un file *.cer* corrispondente da caricare in Azure quando si configura una connessione Point-to-Site. Eseguire il comando seguente, come amministratore, in cui *CertificateName* è il nome che si desidera utilizzare per il certificato.<br><br>Se si esegue l'esempio seguente senza modifiche, come risultato si ottiene un certificato e il corrispondente file *CertificateName.cer*. È possibile trovare il file con estensione cer nella directory da cui è stato eseguito il comando. Il certificato si troverà in Certificati -Utente corrente\\Personale\\Certificati.

    	makecert -sky exchange -r -n "CN=CertificateName" -pe -a sha1 -len 2048 -ss My "CertificateName.cer"

4. Il certificato autofirmato viene utilizzato per creare i certificati client. Quando si carica il file .cer per il certificato autofirmato come parte della configurazione Point-to-Site, si indica ad Azure di ritenere attendibili i certificati client in uso sui computer.<br><br>Alla rete virtuale tramite Point-to-Site può connettersi qualsiasi computer con installato un certificato client anch'esso configurato con le impostazioni client VPN appropriate. Per questo motivo, è opportuno assicurarsi che i certificati client vengono generati e installati solo quando richiesto e che di tale certificato autofirmato è stata creata una copia di backup, archiviata in modo sicuro.
 

## Creare e installare i certificati client

Il certificato autofirmato non è ciò che si installa nei client. È necessario generare un certificato client da un certificato autofirmato. Dopoiché, esportare e installare il certificato client nel computer client. I seguenti passaggi non si applicano specificatamente a un modello di distribuzione. Sono validi sia per Gestione risorse che per il modello classico.

### Parte 1: generare un certificato client da un certificato autofirmato

I passaggi seguenti illustrano come generare un certificato client da un certificato autofirmato. È possibile generare più certificati client dallo stesso certificato. Ogni certificato client può essere esportato e installato nel computer client.

1. Nello stesso computer usato per creare il certificato autofirmato aprire un prompt dei comandi come amministratore.

2. Passare alla directory in cui si vuole salvare il file del certificato client. *CertificateName* fa riferimento al certificato autofirmato che è stato generato. Se si esegue l'esempio riportato di seguito (sostituendo CertificateName con il nome del certificato), il risultato è un certificato client denominato "ClientCertificateName" nell'archivio certificati personale.

3. Digitare il seguente comando:

    	makecert.exe -n "CN=ClientCertificateName" -pe -sky exchange -m 96 -ss My -in "CertificateName" -is my -a sha1

4. Tutti i certificati vengono archiviati nell'archivio Certificati - Utente corrente\\Personale\\Certificati nel computer in uso. È possibile generare tutti i certificati client necessari in base a questa procedura.

### Parte 2: esportare un certificato client

1. Per esportare un certificato client, aprire **certmgr.msc**. Fare clic con il pulsante destro del mouse sul certificato client da esportare, scegliere **Tutte le attività** e quindi fare clic su **Esporta**. Si avvia la procedura di **Esportazione guidata certificati**.

2. Nella procedura guidata, fare clic su **Avanti**, selezionare **Sì, esporta la chiave privata**, quindi fare clic su **Avanti**.

3. Nella pagina **Formato file di esportazione**, è possibile lasciare selezionate le impostazioni predefinite. Quindi fare clic su **Next**.
 
4. Nella pagina **Sicurezza**, è necessario proteggere la chiave privata. Se si sceglie di usare una password, assicurarsi di registrare o ricordare quella impostata per questo certificato. Quindi fare clic su **Next**.

5. In **File da esportare**, fare clic su**Sfoglia** e passare alla posizione in cui si desidera esportare il certificato. Per **Nome file**, assegnare un nome al file del certificato. Quindi fare clic su **Next**.

6. Fare clic su **Fine** per esportare il certificato.

### Parte 3: installare un certificato client

In ogni client che si desidera connettere alla rete virtuale con una connessione da punto a sito deve essere stato installato un certificato client. Questo certificato è aggiuntivo rispetto al pacchetto di configurazione VPN richiesto. La procedura seguente illustra come installare manualmente il certificato client.

1. Individuare e copiare il file *.pfx* nel computer client. Nel computer client, fare doppio clic sul file *.pfx* per installarlo. Lasciare il **Percorso archivio** impostato come **Utente corrente**, quindi fare clic su **Avanti**.

2. Nella pagina **File da importare**, non apportare alcuna modifica. Fare clic su **Avanti**.

3. Nella pagina **Protezione della chiave privata**, immettere la password per il certificato se impostata o verificare che l'entità di sicurezza che installa il certificato sia corretta, quindi fare clic su **Avanti**.

4. Nella pagina **Archivio certificati**, lasciare la posizione come predefinita, quindi fare clic su **Avanti**.

5. Fare clic su **Finish**. In **Avviso di sicurezza** per l'installazione del certificato, fare clic su **Sì**. Il certificato è stato importato correttamente.

## Passaggi successivi

Continuare con la configurazione da punto a sito.

- Per i passaggi del modello di distribuzione **Gestione risorse**, vedere l'articolo relativo a come [configurare una connessione da punto a sito a una rete virtuale con PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md).
- Per i passaggi del modello di distribuzione **classico**, vedere [Configurare una connessione VPN da punto a sito a una rete virtuale](vpn-gateway-point-to-site-create.md).

<!---HONumber=AcomDC_0817_2016-->