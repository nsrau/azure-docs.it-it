<properties
   pageTitle="Configurare un progetto di servizio cloud di Azure con Visual Studio | Microsoft Azure"
   description="Informazioni su come configurare un progetto di servizio cloud di Azure in Visuali Studio, in base ai requisiti specifici per il progetto."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# Configurare un progetto di servizio cloud di Azure con Visual Studio

È possibile configurare un progetto di servizio cloud di Azure, in base ai requisiti specifici per il progetto. È possibile impostare proprietà per il progetto per le categorie seguenti:

- **Pubblicare un servizio cloud in Azure**

  È possibile impostare una proprietà per assicurarsi che un servizio cloud esistente distribuito in Azure non venga eliminato inavvertitamente.

- **Eseguire o eseguire il debug di un servizio cloud nel computer locale**

  È possibile selezionare una configurazione del servizio da usare e indicare se si desidera avviare l'emulatore di archiviazione di Azure.

- **Convalidare un pacchetto del servizio cloud quando viene creato**

  È possibile decidere di trattare tutti gli avvisi come errori in modo da assicurarsi che il pacchetto del servizio cloud venga distribuito senza problemi. Ciò riduce i tempi di attesa nel caso si scopra che si è verificato un errore dopo la distribuzione.

Nella figura seguente viene illustrato come selezionare una configurazione da usare durante l'esecuzione o il debug del servizio cloud in locale. È possibile impostare tutte le proprietà di progetto necessarie da questa finestra, come illustrato nella figura.

![Configurare un progetto Microsoft Azure](./media/vs-azure-tools-configuring-an-azure-project/IC713462.png)

## Per configurare un progetto di servizio cloud di Azure

1. Per configurare un progetto di servizio cloud da **Esplora soluzioni**, aprire il menu di scelta rapida per il progetto di servizio cloud e quindi scegliere **Proprietà**.

  Una pagina con il nome del progetto di servizio cloud viene visualizzata nell'editor di Visual Studio.

1. Scegliere la scheda **Sviluppo**.

1. Per assicurarsi di non eliminare inavvertitamente una distribuzione esistente in Azure, nell'elenco Chiedi conferma prima di eliminare una distribuzione esistente scegliere **True**.

1. Per selezionare la configurazione del servizio che si desidera usare durante l'esecuzione o il debug del servizio cloud in locale, nell'elenco **Configurazione servizio** scegliere la configurazione del servizio.

  >[AZURE.NOTE] Se si desidera creare una configurazione del servizio da usare, vedere Procedura: Gestire i profili e le configurazioni del servizio. Se si desidera modificare una configurazione del servizio per un ruolo, vedere [Procedura: Configurare i ruoli di un servizio cloud di Azure con Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).

1. Per avviare l'emulatore di archiviazione di Azure durante l'esecuzione o il debug del servizio cloud in locale, in **Avvia l'emulatore di archiviazione di Microsoft Azure** scegliere **True**.

1. Per assicurarsi che non sia possibile pubblicare se sono presenti errori di convalida del pacchetto, in **Considera gli avvisi come errori** scegliere **True**.

1. Per assicurarsi che il ruolo Web usi la stessa porta a ogni avvio in locale in IIS Express, in **Usa le porte del progetto Web** scegliere **True**. Per usare una porta specifica per un particolare progetto Web, aprire il menu di scelta rapida per il progetto Web, scegliere la scheda **Proprietà**, scegliere la scheda **Web** e modificare il numero di porta nell'impostazione **URL progetto** nella sezione **IIS Express**. Ad esempio, immettere `http://localhost:14020` come URL di progetto.

1. Per salvare le modifiche apportate alle proprietà del progetto di servizio cloud, scegliere il pulsante **Salva** sulla barra degli strumenti.

## Passaggi successivi

Per altre informazioni su come configurare i progetti di servizio cloud in Visual Studio, vedere [Configurazione di un progetto Azure usando configurazioni del servizio multiple](vs-azure-tools-multiple-services-project-configurations.md).

<!---HONumber=AcomDC_0817_2016-->