<properties
	pageTitle="Creare un indice di Ricerca di Azure nel portale | Microsoft Azure"
	description="Aggiungere un indice al servizio di Ricerca di Azure compilando le definizioni di campo nel portale di Azure"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="09/08/2015"
	ms.author="heidist"/>

# Creare un indice di Ricerca di Azure nel portale di Azure

È possibile rapidamente creare un prototipo di un indice di Ricerca di Azure creandone uno nel portale di Azure. L’utilizzo del portale è ideale per test di verifica dei concetti, ma può inoltre essere utilizzato per visualizzare le definizioni di schema e l'utilizzo delle risorse per qualsiasi indice distribuito al servizio.

Per completare questa attività, assicurarsi di disporre di un servizio di Ricerca di Azure pronto da utilizzare. Vedere [creare un servizio di Ricerca di Azure nel portale](search-create-service-portal.md) per assistenza su come impostarlo.

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Aprire il dashboard relativo al servizio Ricerca di Azure. Di seguito alcuni modi per trovare il dashboard.
	- Nell'indice, fare clic su **Home page**. La home page dispone di sezioni per ogni servizio incluso nella sottoscrizione. Fare clic sull'icona per aprire il dashboard del servizio.
	- Nell'indice, fare clic su **Sfoglia tutte le** > **Filtrare in base a** > **Ricerca servizi** per trovare il servizio di ricerca nell'elenco.

3. Nel dashboard del servizio, verrà visualizzata una barra dei comandi nella parte superiore, compresa una per l’**aggiunta dell’indice**.

	Controllare il livello di prezzo. Se si dispone della versione gratuita, è possibile includere i fino a 3 indici. Potrebbe essere necessario eliminarne uno per liberare spazio.

     ![][1]

4. Per eliminare un indice, fare clic su uno di essi per aprire un pannello. Fare clic su **Elimina**.

     ![][2]

5. Per creare un nuovo indice nel portale, fare clic su **Aggiungi indice** e assegnarvi un nome, ad esempio *hotel*.

	Potrebbe occorrere un minuto per creare l'indice, ma quando sarà pronto da utilizzare, verrà visualizzato nell'elenco degli indici.

6. Fare clic su **hotel** per aprire il pannello di definizione degli indici.

	Quando si crea un indice nel portale, un (ID) campo obbligatorio viene creato automaticamente. Questo è il campo chiave utilizzato per identificare in modo univoco ogni documento. Esiste un solo campo per ogni chiave (nessuna chiave composta) ed è sempre una stringa.

	Se si desidera rinominare il campo chiave, è importante eseguire questo passaggio in questo momento durante la creazione dell'indice. Non sarà possibile rinominare un campo dopo la creazione dell'indice.

	![][3]

7. Per modificare il nome del campo, fare clic sulla freccia a destra nell'elenco campi.

8. Sostituire **ID** con **Idhotel**.

9. Fare clic su **OK** su ogni pannello (campi e indice) per creare l'indice.

## Aggiungere campi

In Ricerca di Azure gli attributi di indice, quali searchable, facetable e filterable sono abilitati per impostazione predefinita. In genere, quando si imposta questi attributi, si disattivano i comportamenti di ricerca che non hanno senso (ad esempio, l'ordinamento o esplorazione in base a facet di una descrizione).

Il portale è diverso. Nel portale i comportamenti di ricerca sono disattivati per impostazione predefinita in modo che sia possibile selezionare tutti i comportamenti che si applicano, da campo a campo.

1. Fare clic sull’opzione per l’**aggiunta o la modifica dei campi** per aggiungere ulteriori campi. In questo esercizio, è possibile ricreare il *hotel* indice descritto nell'articolo [come utilizzare Fiddler con Ricerca di Azure](search-fiddler.md).

	![][4]

2. Aggiungere e configurare i campi per completare lo schema.

	![][5]

	Revisione [regole di denominazione](https://msdn.microsoft.com/library/azure/dn857353.aspx) e [tipi di dati supportati](https://msdn.microsoft.com/library/azure/dn798938.aspx) per informazioni sui nomi di campo e tipi di riferimento.

    Attributi dell'indice sono i seguenti elementi:

	- **Retrievable** specifica se un campo può essere restituito in un risultato di ricerca.
	- **Filterable** consente il campo da utilizzare in **$filter** query.
	- **Sortable** consente il campo da utilizzare come opzione di ordinamento.
	- **Facetable** consente a un campo da utilizzare in una struttura di spostamento di collaborazione per il filtro autodidattico. In genere i campi contenenti valori ricorrenti che è possibile utilizzare per raggruppare più documenti (ad esempio, più documenti che rientrano in un singolo prodotto o una categoria di servizi) funzionano meglio come facet.
	- **Chiave** è l'ID univoco di ogni documento utilizzato per cercare di documento. Ogni indice deve avere una chiave. Un solo campo può essere la chiave e deve essere impostata su Edm.
	- **Ricercabile** il campo viene contrassegnato come full-text è possibile eseguire ricerche.

3. Per rimuovere tutti i campi non desiderati, mouse e selezionare **eliminare**.

4. Fare clic su **OK** Salva l'indice appena definito, quindi fare clic su **OK** nella pagina di indice Aggiungi per creare l'indice.


## Passaggi successivi

Anche se l'indice è definito, non potrà essere utilizzato fino a quando non si caricano documenti. Supponendo di stare ricreando l’indice Hotel, utilizzato per scopi di test, è possibile caricare facilmente un numero ridotto di documenti per tale indice in [Fiddler](search-fiddler.md) utilizzando le istruzioni nella sezione Carica documenti di [Come utilizzare Fiddler con Ricerca di Azure](search-fiddler.md). È quindi possibile seguire i passaggi rimanenti dell'articolo per eseguire alcune query.

Una volta acquisita familiarità con l'indice di base, prendere in considerazione l'aggiunta di un analizzatore del linguaggio o componente per il suggerimento per aggiungere supporto multilingue o suggerimenti di completamento automatico. Entrambe le funzionalità vengono specificate nello schema di indice. Vedere [Supporto linguistico](https://msdn.microsoft.com/elibrary/azure/dn879793.aspx) e [Creare un indice](https://msdn.microsoft.com/library/azure/dn798941.aspx) per ulteriori informazioni.

<!--Image references-->
[1]: ./media/search-create-index-portal/AzureSearch-PortalIndex-1.PNG
[2]: ./media/search-create-index-portal/AzureSearch-PortalIndex-2.PNG
[3]: ./media/search-create-index-portal/AzureSearch-PortalIndex-3.PNG
[4]: ./media/search-create-index-portal/AzureSearch-PortalIndex-4.PNG
[5]: ./media/search-create-index-portal/AzureSearch-PortalIndex-5.PNG

<!---HONumber=Sept15_HO2-->