<properties 
	pageTitle="Eseguire una distribuzione da Visual Studio" 
	description="Creare un progetto in Visual Studio per gestire l'app per la logica." 
	authors="stepsic-microsoft-com" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/15/2015"
	ms.author="stepsic"/>
	
# Eseguire una distribuzione da Visual Studio

Anche se il [portale di Azure](https://portal.azure.com) rappresenta un ottimo modo per progettare e gestire le app per la logica, è possibile distribuire l'app per la logica anche da Visual Studio. Questa scelta offre due funzionalità principali:

- Archiviare all'app per la logica insieme ad altre risorse nella soluzione in modo che possa contenere tutti gli aspetti dell'applicazione
- Controllare la definizione dell'app per la logica archiviata nel codice sorgente in modo da poter usare TFS o Git per tenere traccia delle relative revisioni 

Per eseguire la procedura seguente è necessario avere installato Azure SDK 2.7 o versione successiva. L'[SDK più recente per Visual Studio](http://azure.microsoft.com/downloads/) è disponibile qui.

## Creare un progetto

1. Andare al menu **File** e selezionare **Nuovo** > **Progetto** (in alternativa, è possibile selezionare **Aggiungi** e quindi **Nuovo progetto** per aggiungerlo a una soluzione esistente) ![File menu](./media/app-service-logic-deploy-from-vs/filemenu.png)

2. Nella finestra di dialogo **Cloud** selezionare **Gruppo di risorse di Azure**. Digitare un **Nome** e fare clic su **OK**. ![Add new project](./media/app-service-logic-deploy-from-vs/addnewproject.png)

3. Ora è necessario selezionare **App per la logica** o **App per la logica + App API**. Se si seleziona **App per la logica** è necessario puntare delle API esistenti. Se si seleziona **App per la logica + App API** è anche possibile creare contemporaneamente un'app API nuova e vuota. ![Select Azure template](./media/app-service-logic-deploy-from-vs/selectazuretemplate.png)

4. Dopo aver selezionato il **Modello** premere **OK**.

A questo punto il progetto di app per la logica verrà aggiunto alla soluzione. Verrà visualizzata la distribuzione in Esplora soluzioni: ![Distribuzione](./media/app-service-logic-deploy-from-vs/deployment.png)

## Configurazione dell'app per la logica

Dopo aver creato un progetto è possibile modificare la definizione dell'app per la logica all'interno di Visual Studio. Fare clic sul file JSON in Esplora soluzioni. Verrà visualizzata una definizione di segnaposto che è possibile compilare con la logica dell'applicazione.

Si consiglia di usare **parametri** in tutta la definizione. L'uso dei parametri è utile se si vuole effettuare la distribuzione in un ambiente sia di sviluppo che di produzione. In tal caso, è necessario inserire i dati di configurazione di tutte le specifiche dell'ambiente nel file `.param` e i parametri anziché le stringhe effettive.

Attualmente Visual Studio non dispone di una finestra di progettazione incorporata JSON; pertanto, se si vuole usare un'interfaccia grafica (anziché la scrittura di JSON), è necessario usare il portale di Azure.

Se in precedenza è stata creata un'app per la logica all'interno del portale di Azure e ora si desidera archiviarla nel controllo del codice sorgente, esistono tre possibili modalità per eseguire questa operazione: - Passare a **Visualizzazione Codice** nel portale e copiare la definizione. - Usare le app per la logica [API REST](https://msdn.microsoft.com/library/azure/dn948510.aspx) per ottenere la definizione. - Usare [la PowerShell di Gestione risorse di Azure](../powershell-azure-resource-manager.md), in particolare il comando [`Get-AzureResource`](https://msdn.microsoft.com/library/dn654579.aspx) per scaricare la definizione.

## Distribuzione dell'app per la logica

Infine, dopo aver configurato l'app è possibile distribuire direttamente da Visual Studio in pochi passaggi.

1. Fare clic con il pulsante destro sulla distribuzione in Esplora soluzioni e passare a **Distribuisci** > **Nuova distribuzione...** ![New deployment](./media/app-service-logic-deploy-from-vs/newdeployment.png)

2. Verrà richiesto di accedere alla sottoscrizione di Azure.

3. A questo punto è necessario scegliere i dettagli del gruppo di risorse in cui si vuole distribuire l'app per la logica. ![Deploy to resource group](./media/app-service-logic-deploy-from-vs/deploytoresourcegroup.png)

    Assicurarsi di selezionare il modello e i file dei parametri appropriati per il gruppo di risorse (ad esempio, se si effettua la distribuzione in un ambiente di produzione è opportuno scegliere il file dei parametri di produzione).
    
4. Lo stato della distribuzione sarà visualizzato nella finestra **Output** (potrebbe essere necessario scegliere **Provisioning Azure**. ![Output](./media/app-service-logic-deploy-from-vs/output.png)

In futuro sarà possibile modificare l'app per la logica nel controllo del codice sorgente e usare Visual Studio per distribuire nuove versioni. Si noti che se si modifica la definizione direttamente nel portale di Azure, alla successiva distribuzione da Visual Studio tali modifiche saranno sottoposte a sovrascrittura.

Se non si vuole usare Visual Studio, ma si desidera comunque utilizzare gli strumenti necessari per distribuire l'app per la logica dal controllo di origine, è possibile usare l'[API](https://msdn.microsoft.com/library/azure/dn948510.aspx) o [PowerShell](../powershell-azure-resource-manager.md) direttamente per automatizzare le distribuzioni.

<!---HONumber=Oct15_HO4-->