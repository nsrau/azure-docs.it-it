<properties
   pageTitle="Usare Visual Studio COde con i modelli di Resource Manager | Microsoft Azure"
   description="Viene illustrato come configurare Visual Studio Code per creare modelli di Azure Resource Manager."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="cmatskas"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/29/2016"
   ms.author="chmatsk;tomfitz"/>

# Utilizzo dei modelli di Azure Resource Manager nel codice di Visual Studio

I modelli di Azure Resource Manager sono file JSON che descrivono una risorsa e le relative dipendenze. Questi file possono talvolta essere grandi e complessi, perciò è importante avere strumenti di supporto. Visual Studio Code è un nuovo editor di codice, leggero, open source e multipiattaforma. Supporta la creazione e la modifica dei modelli di Resource Manager tramite una [nuova estensione](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools). Visual Studio Code viene eseguito ovunque e non richiede l'accesso a Internet, a meno che non si vogliano distribuire i modelli di Resource Manager.

Se Visual Studio Code non è già disponibile, è possibile installarlo da [https://code.visualstudio.com/](https://code.visualstudio.com/).

## Installare l'estensione per Resource Manager

Per utilizzare i modelli JSON in Visual Studio Code, è necessario installare un'estensione. I passaggi seguenti consentono di scaricare e installare il supporto linguistico per i modelli JSON di Resource Manager:

1. Avviare Visual Studio Code
2. Aprire Apertura rapida (CTRL+P)
3. Eseguire il comando seguente:

        ext install azurerm-vscode-tools

4. Riavviare Visual Studio Code quando richiesto per abilitare l'estensione.

 L'operazione è stata completata.

## Configurare i frammenti di codice di Resource Manager

Nei passaggi precedenti è stato installato il supporto degli strumenti, ma ora è necessario configurare Visual Studio Code per l'uso di frammenti di codice del modello JSON.

1. Copiare il contenuto del file dal repository [azure-xplat-arm-tooling](https://raw.githubusercontent.com/Azure/azure-xplat-arm-tooling/master/VSCode/armsnippets.json) negli Appunti.
2. Avviare Visual Studio Code
3. In Visual Studio Code è possibile aprire il file di frammenti JSON passando a **File** -> **Preferences** (Preferenze) -> **User Snippets** (Frammenti utente) -> **JSON** oppure premendo **F1** e digitando **preferences** (preferenze) fino a quando non è possibile selezionare **Preferences: Snippets** (Preferenze: frammenti).

    ![frammenti di preferenze](./media/resource-manager-vs-code/preferences-snippets.png)

    Nelle opzioni selezionare **JSON**.

    ![selezionare json](./media/resource-manager-vs-code/select-json.png)

4. Incollare il contenuto del file nel passaggio 1 nel file di frammenti di codice utente prima della "}" finale.
5. Assicurarsi che il codice JSON abbia un aspetto corretto e non siano presenti errori di ortografia.
6. Salvare e chiudere il file di frammenti di codice utente.

È tutto ciò che serve per iniziare a usare i frammenti di codice di Resource Manager. Successivamente, verrà eseguito il test di questa configurazione.

## Utilizzare il modello in Visual Studio Code

Il modo più semplice per iniziare a utilizzare un modello consiste nello scegliere uno dei modelli di avvio rapido disponibili in [Github](https://github.com/Azure/azure-quickstart-templates) oppure usarne uno personalizzato. È possibile [esportare facilmente un modello ](resource-manager-export-template.md) per uno qualsiasi dei gruppi di risorse tramite il portale.

1. Se è stato esportato un modello da un gruppo di risorse, aprire i file estratti in Visual Studio Code.

    ![visualizzare i file](./media/resource-manager-vs-code/show-files.png)

2. Aprire il file template.json per poterlo modificare e aggiungere alcune risorse aggiuntive. Dopo **"resources": [** premere INVIO per iniziare una nuova riga. Se si digita **arm**, verrà visualizzato un elenco di opzioni. Queste opzioni sono i frammenti di codice del modello installato. L'aspetto dovrebbe risultare simile al seguente:

    ![visualizzare frammenti di codice](./media/resource-manager-vs-code/type-snippets.png)

3. Scegliere il frammento desiderato. Per questo articolo si sceglierà **ip arm** per creare un nuovo indirizzo IP pubblico. Inserire una virgola dopo la parentesi di chiusura "}" della risorsa appena creata per assicurarsi che la sintassi del modello sia valida.

     ![aggiungere una virgola](./media/resource-manager-vs-code/add-comma.png)

4. Visual Studio Code include IntelliSense. Quando si modificano i modelli, Visual Studio Code suggerisce i valori disponibili. Ad esempio, per aggiungere una sezione di variabili al modello, aggiungere **""** (due virgolette doppie) e premere **CTRL+BARRA SPAZIATRICE** tra le virgolette. Verranno visualizzata le opzioni che includono **variabili**.

    ![aggiungere variabili](./media/resource-manager-vs-code/add-variables.png)

5. IntelliSense può anche suggerire valori o funzioni disponibili. Per impostare una proprietà su un valore di parametro, creare un'espressione con **""** e **CTRL+BARRA SPAZIATRICE**. È possibile iniziare a digitare il nome di una funzione. Premere **TAB** quando è stata trovata la funzione desiderata.

    ![aggiungere un parametro](./media/resource-manager-vs-code/select-parameters.png)

6. Premere di nuovo **CTRL+BARRA SPAZIATRICE** all'interno della funzione per visualizzare un elenco di parametri disponibili all'interno del modello.

    ![aggiungere un parametro](./media/resource-manager-vs-code/select-avail-parameters.png)

7. In caso di problemi di convalida dello schema nel modello, nell'editor saranno visibili le familiari indicazioni del controllo ortografia. È possibile visualizzare l'elenco di errori e avvisi premendo **CTRL+MAIUSC+M** o selezionando i glifi sulla barra di stato in basso a sinistra.

    ![errors](./media/resource-manager-vs-code/errors.png)

    La convalida del modello consente di rilevare problemi di sintassi. Tuttavia, possono anche essere visualizzati errori che è possibile ignorare. In alcuni casi, l'editor confronta il modello rispetto a uno schema non aggiornato e quindi segnala un errore anche se è corretto. Si supponga, ad esempio, che una funzione sia stato aggiunto di recente a Resouce Manager, ma lo schema non è stato aggiornato. L'editor segnala un errore anche se la funzione, in effetti, funziona correttamente durante la distribuzione.

    ![Messaggio di errore](./media/resource-manager-vs-code/unrecognized-function.png)

## Distribuire le nuove risorse

Quando il modello è pronto, è possibile distribuire le nuove risorse seguendo le istruzioni riportate di seguito:

### Windows

1. Aprire un prompt dei comandi di PowerShell
2. Per accedere digitare:

        Login-AzureRmAccount 

3. Se sono disponibili più sottoscrizioni, ottenere un elenco delle sottoscrizioni con:

        Get-AzureRmSubscription

    Selezionare quindi la sottoscrizione da usare.
   
        Select-AzureRmSubscription -SubscriptionId <Subscription Id>

4. Aggiornare i parametri nel file parameters.json
5. Eseguire Deploy.ps1 per distribuire il modello in Azure

### OSX/Linux

1. Aprire una finestra del terminale
2. Per accedere digitare:

        azure login 

3. Se sono disponibili più sottoscrizioni, selezionare la sottoscrizione appropriata con:

        azure account set <subscriptionNameOrId> 

4. Aggiornare i parametri nel file parameters.json.
5. Per distribuire il modello, eseguire:

        azure group deployment create -f <PathToTemplate> 

## Passaggi successivi

- Per altre informazioni sui modelli, vedere [Creazione di modelli di Azure Resource Manager](resource-group-authoring-templates.md).
- Per altre informazioni sulle funzioni del modello, vedere [Funzioni del modello di Azure Resource Manager](resource-group-template-functions.md).
- Per altri esempi dell'uso di Visual Studio Code, vedere [Build cloud apps with Visual Studio Code](https://github.com/Microsoft/HealthClinic.biz/wiki/Build-cloud-apps-with-Visual-Studio-Code) (Compilare app per cloud con Visual Studio Code) della [demo](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/) [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 Connect. Per altre guide introduttive della demo HealthClinic.biz, vedere [Azure Developer Tools Quickstarts](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts) (Guide introduttive agli strumenti di sviluppo di Azure).

<!---HONumber=AcomDC_0803_2016-->