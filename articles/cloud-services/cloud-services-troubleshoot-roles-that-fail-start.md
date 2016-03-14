<properties
   pageTitle="Risolvere i problemi dei ruoli che non vengono avviati | Microsoft Azure"
   description="Informazioni su alcuni motivi comuni del mancato avvio di un ruolo del servizio cloud. Include anche soluzioni per questi problemi."
   services="cloud-services"
   documentationCenter=""
   authors="dalechen"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="02/25/2016"
   ms.author="daleche" />

# Risolvere i problemi dei ruoli del servizio cloud che non vengono avviati

Ecco alcuni problemi e soluzioni comini correlati ai ruoli di Servizi cloud di Azure che non vengono avviati.

## Contattare il supporto tecnico di Azure

Se è necessaria ulteriore assistenza in qualsiasi punto in questo articolo, è possibile contattare gli esperti di Azure su [MSDN Azure e i forum di overflow dello stack](https://azure.microsoft.com/support/forums/).

In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Accedere al [sito del Supporto tecnico di Azure](http://azure.microsoft.com/support/options/) e fare clic su **Ottenere supporto**. Per informazioni sull'uso del supporto per Azure, vedere le [Domande frequenti sul supporto tecnico di Microsoft Azure](http://azure.microsoft.com/support/faq/).

## File DLL o dipendenze mancanti

I problemi dovuti a ruoli che non rispondono e ruoli che passano in ciclo tra gli stati **Inizializzazione**, **Occupato** e **Arresto** possono essere provocati da file DLL o assembly mancanti.

I sintomi di file DLL o assembly mancati includono i seguenti:

- L'istanza del ruolo passa in ciclo tra gli stati **Inizializzazione**, **Occupato** e **Arresto**.
- L'istanza del ruolo è passata allo stato **Pronto**, ma, se si passa all'applicazione Web, la pagina non viene visualizzata.

Sono disponibili diversi metodi consigliati per esaminare questi problemi.

## Diagnosticare i problemi di DLL mancanti in un ruolo Web

Quando si passa a un sito Web distribuito in un ruolo Web e il browser visualizza un errore server simile al seguente, è possibile che indichi una DLL mancante.

![Errore del server nell'applicazione '/'.](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503388.png)

## Diagnosticare i problemi disabilitando gli errori personalizzati

Per visualizzare informazioni sugli errori più completi, configurare il file web.config per il ruolo Web per poter disattivare la modalità di errore personalizzata e ridistribuire il servizio.

Per visualizzare errori più completi senza usare Desktop remoto:

1. Aprire la soluzione in Microsoft Visual Studio.

2. In **Esplora soluzioni** individuare il file web.config e aprirlo.

3. Nel file web.config individuare la sezione system.web e aggiungere la riga seguente:

    ```xml
    <customErrors mode="Off" />
    ```

4. Salvare il file.

5. Ricreare il pacchetto e ridistribuire il servizio.

Quando il servizio viene ridistribuito, verrà visualizzato un messaggio di errore con il nome dell'assembly o della DLL mancante.

## Diagnosticare i problemi visualizzando l'errore in remoto

È possibile usare Desktop remoto per accedere al ruolo e visualizzare informazioni sugli errori più complete in remoto. Seguire questa procedura per visualizzare gli errori usando Desktop remoto:

1. Verificare che sia installato Azure SDK 1.3 o versione successiva.

2. Durante la distribuzione della soluzione con Visual Studio, scegliere "Configura connessioni Desktop remoto". Per altre informazioni sulla configurazione della connessione Desktop remoto, vedere [Uso di Desktop remoto con i ruoli Azure](../vs-azure-tools-remote-desktop-roles.md).

3. Quando lo stato dell'istanza è **Pronto**, nel portale di Microsoft Azure classico fare clic su una delle istanze del ruolo.

4. Fare clic sull'icona **Connetti** nell'area **Accesso remoto** della barra multifunzione.

5. Accedere alla macchina virtuale usando le credenziali specificate durante la configurazione di Desktop remoto.

6. Aprire una finestra di comando.

7. Digitare `IPconfig`.

8. Annotare il valore dell'indirizzo IPV4.

9. Aprire Internet Explorer.

10. Digitare l'indirizzo e il nome dell'applicazione Web, ad esempio `http://<IPV4 Address>/default.aspx`.

Se si passa al sito Web, ora verranno restituiti messaggi di errore più espliciti:

* Errore del server nell'applicazione '/'.

* Descrizione: Eccezione non gestita durante l'esecuzione della richiesta Web corrente. Per altre informazioni sull'errore e sul suo punto di origine nel codice, vedere l'analisi dello stack.

* Dettagli dell'eccezione: System.IO.FIleNotFoundException: Impossibile caricare il file o l'assembly 'Microsoft.WindowsAzure.StorageClient, Version=1.1.0.0, Culture=neutral, PublicKeyToken=31bf856ad364e35' o una delle relative dipendenze. Non è possibile trovare il file specificato.

Ad esempio:

![Errore esplicito del server nell'applicazione '/'](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503389.png)

## Diagnosticare i problemi usando l'emulatore di calcolo

È possibile usare l'emulatore di calcolo di Microsoft Azure per diagnosticare e risolvere i problemi relativi a dipendenze mancanti e gli errori del file web.config.

Per ottenere risultati ottimali con questo metodo di diagnosi, è consigliabile usare un computer o una macchina virtuale in cui è presente un'installazione pulita di Windows. Per simulare in modo ottimale l'ambiente Azure, usare Windows Server 2008 R2 x64.

1. Installare la versione autonoma di [Azure SDK](https://azure.microsoft.com/downloads/)

2. Nel computer di sviluppo compilare il progetto di servizio cloud.

3. In Esplora risorse passare alla cartella bin\\debug del progetto di servizio cloud.

4. Copiare la cartella con estensione csx e il file con estensione cscfg nel computer usato per eseguire il debug dei problemi.

5. Nel computer pulito aprire una finestra del prompt dei comandi di Azure SDK e digitare `csrun.exe /devstore:start`.

6. Al prompt dei comandi digitare `run csrun <path to .csx folder> <path to .cscfg file> /launchBrowser`.

7. All'avvio del ruolo, verranno visualizzate informazioni dettagliate sull'errore in Internet Explorer. È anche possibile usare gli strumenti di risoluzione dei problemi standard di Windows per diagnosticare ulteriormente il problema.

## Diagnosticare i problemi usando IntelliTrace

Per i ruoli di lavoro e i ruoli Web che usano .NET Framework 4, è possibile usare [IntelliTrace](https://msdn.microsoft.com/library/dd264915.aspx), disponibile in [Microsoft Visual Studio Ultimate](https://www.visualstudio.com/products/visual-studio-ultimate-with-MSDN-vs).

Seguire questa procedura per distribuire il servizio con IntelliTrace abilitato:

1. Verificare che sia installato Azure SDK 1.3 o versione successiva.

2. Distribuire la soluzione usando Visual Studio. Durante la distribuzione selezionare la casella di controllo **Abilita IntelliTrace per ruoli .NET 4**.

3. Dopo l'avvio dell'istanza, aprire **Esplora server**.

4. Espandere il nodo **Azure\\Servizi cloud** e individuare la distribuzione.

5. Espandere la distribuzione finché non vengono visualizzate le istanze del ruolo. Fare clic con il pulsante destro del mouse su una delle istanze.

6. Scegliere **Visualizza log IntelliTrace**. Verrà visualizzato il **Riepilogo IntelliTrace**.

7. Individuare la sezione delle eccezioni del riepilogo. Se sono presenti eccezioni, la sezione sarà denominata **Dati eccezione**.

8. Espandere **Dati eccezione**e cercare errori **System.IO.FileNotFoundException** analoghi al seguente:

![Dati eccezione, assembly o file mancante](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503390.png)

## Risolvere DLL e assembly mancanti

Per risolvere errori di assembly e DLL mancanti, seguire questa procedura:

1. Aprire la soluzione in Visual Studio.

2. In **Esplora soluzioni** aprire la cartella **Riferimenti**.

3. Fare clic sull'assembly specificato nell'errore.

4. Nel riquadro **Proprietà** individuare la proprietà **Copia localmente** e impostare il valore su **True**.

5. Ridistribuire il servizio cloud.

Dopo avere verificato che tutti gli errori sono stati corretti, è possibile distribuire il servizio senza selezionare la casella di controllo **Abilita IntelliTrace per ruoli .NET 4**.

## Passaggi successivi

Altri [articoli sulla risoluzione dei problemi](..\?tag=top-support-issue&service=cloud-services) per i servizi cloud.

Per informazioni su come risolvere i problemi dei ruoli del servizio cloud usando i dati di diagnostica del calcolo Azure PaaS, vedere la [serie di blog di Kevin Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

<!---HONumber=AcomDC_0302_2016-->