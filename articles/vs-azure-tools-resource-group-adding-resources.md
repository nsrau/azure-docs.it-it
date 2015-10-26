<properties 
   pageTitle="Aggiunta di risorse a un gruppo di risorse di Azure"
   description="Informazioni su come aggiungere risorse a un gruppo di risorse di Azure con Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="kempb"
   manager="douge"
   editor="tglee" />
<tags 
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/13/2015"
   ms.author="kempb" />

# Aggiunta di risorse a un gruppo di risorse di Azure

Se è necessario aggiungere altre risorse a un gruppo di risorse, è possibile eseguire questa operazione nella finestra Struttura JSON in Visual Studio.

## Aggiunta di risorse a un gruppo di risorse

### Per aggiungere risorse a un gruppo di risorse

1. In Esplora soluzioni scegliere il file JSON per il gruppo di risorse di Azure. Il file JSON viene visualizzato nell'editor e accanto all'editor viene visualizzata anche la finestra **Struttura JSON**. Se la si chiude, la finestra Struttura JSON verrà aperta di nuovo automaticamente solo quando si sceglie il comando Mostra struttura dal menu di scelta rapida di un file di modello JSON (non un file dei parametri).

    ![File JSON per il gruppo di risorse di Azure](./media/vs-azure-tools-resource-group-adding-resources/arm-json-file.png)

1. Nella finestra **Struttura JSON** scegliere il nodo **risorse** e quindi scegliere il comando **Aggiungi nuova risorsa** dal menu di scelta rapida oppure fare clic sul pulsante **Aggiungi risorsa** nella parte superiore della finestra Struttura JSON.

    ![Aggiunta di una nuova risorsa a un gruppo di risorse](./media/vs-azure-tools-resource-group-adding-resources/arm-add-resource.png)

1. Nell'elenco di risorse nella finestra di dialogo **Aggiungi risorsa** scegliere la risorsa da aggiungere, fornire le informazioni richieste dalla risorsa e quindi fare clic sul pulsante **Aggiungi**. Ad esempio, se si aggiunge un account di archiviazione, sarà necessario fornire un nome di base per i parametri che verranno creati automaticamente.
 
    ![Finestra di dialogo Aggiungi risorsa](./media/vs-azure-tools-resource-group-adding-resources/arm-add-resource-dialog.png)

    La risorsa viene aggiunta agli elenchi di risorse nella finestra **Struttura JSON** e il nuovo JSON che rappresenta la risorsa viene visualizzato nel file. È anche possibile che vengano aggiunti parametri e/o variabili correlati.


    ![Risorsa aggiunta al file JSON](./media/vs-azure-tools-resource-group-adding-resources/arm-add-resource-json.png)

1. Distribuire la nuova risorsa nel gruppo di risorse di Azure. Nel menu di scelta rapida del progetto del gruppo di risorse in Esplora soluzioni scegliere **Distribuisci** e quindi scegliere il nome del gruppo di risorse.

    ![Gruppo di risorse di Azure distribuito](./media/vs-azure-tools-resource-group-adding-resources/deploy-arm-resource-group.png)

    Verrà visualizzata la finestra di dialogo **Distribuisci in gruppo di risorse**.


1. Scegliere il pulsante **Distribuisci**.

1. Se l'utente deve specificare alcuni parametri, verrà visualizzata la finestra di dialogo **Modifica parametri**. Immettere eventuali parametri obbligatori e quindi scegliere il pulsante **Salva**. La nuova risorsa viene distribuita nel gruppo di risorse di Azure.

## Vedere anche

[Creazione e distribuzione di progetti di distribuzione Gruppo di risorse di Azure](http://go.microsoft.com/fwlink/p/?LinkID=623073)

[Cmdlet di Gestione risorse di Azure](https://msdn.microsoft.com/library/dn654592.aspx)

[Uso di Windows PowerShell con Gestione risorse di Azure](../powershell-azure-resource-manager/)

[Video di Channel9: Gestione risorse di Azure](http://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B224#fbid=)

<!---HONumber=Oct15_HO3-->