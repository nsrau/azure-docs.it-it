<properties 
	pageTitle="Creare un indice di Ricerca di Azure nel portale" 
	description="Aggiungere un indice al servizio di Ricerca di Azure compilando le definizioni di campo nel portale di gestione" 
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
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="04/27/2015" 
	ms.author="heidist"/>

# Creare un indice di Ricerca di Azure nel portale

È possibile rapidamente creare un prototipo di un indice di Ricerca di Azure creandone uno nel portale di gestione di Azure. L’utilizzo del portale è ideale per test di verifica dei concetti, ma può inoltre essere utilizzato per visualizzare le definizioni di schema e l'utilizzo delle risorse per qualsiasi indice distribuito al servizio.

Per completare questa attività, assicurarsi di disporre di un servizio di Ricerca di Azure pronto da utilizzare. Vedere [creare un servizio di Ricerca di Azure nel portale](search-create-service-portal.md) per assistenza su come impostarlo.

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Aprire il dashboard relativo al servizio Ricerca di Azure. Di seguito alcuni modi per trovare il dashboard.
	- Nell'indice, fare clic su **Home page**. La home page dispone di sezioni per ogni servizio incluso nella sottoscrizione. Fare clic sull'icona per aprire il dashboard del servizio.
	- Nell'indice fare clic su **Sfoglia** | **Filtra per** | **Servizi di ricerca** per trovare il servizio di ricerca nell'elenco. 

3. Nel dashboard del servizio, verrà visualizzata una barra dei comandi nella parte superiore, compresa una per l’**aggiunta dell’indice**.
	
	Controllare il livello di prezzo. Se si dispone della versione gratuita, è possibile includere i fino a 3 indici. Potrebbe essere necessario eliminarne uno per liberare spazio.

     ![][1]

4. Per eliminare un indice, fare clic su uno di essi per aprire un pannello. Fare clic su **Elimina**.

     ![][2]

5. Per creare un nuovo indice nel portale, fare clic su **Aggiungi indice** e assegnarvi un nome, ad esempio *hotel*.

	Potrebbe occorrere un minuto per creare l'indice, ma quando sarà pronto da utilizzare, verrà visualizzato nell'elenco degli indici.

6. Fare clic su *hotel* per aprire il pannello di definizione degli indici.

	Quando si crea un indice nel portale, un (ID) campo obbligatorio viene creato automaticamente. Questo è il campo chiave utilizzato per identificare in modo univoco ogni documento. Esiste un solo campo per ogni chiave (nessuna chiave composta) ed è sempre una stringa.

	Se si desidera rinominare il campo chiave, è importante eseguire questo passaggio in questo momento durante la creazione dell'indice. Non sarà possibile rinominare un campo dopo la creazione dell'indice.

	![][3]

7. Per modificare il nome del campo, fare clic sulla freccia a destra nell'elenco campi.

8. Sostituire *ID* con *Idhotel*.

9. Fare clic su **OK** su ogni pannello (campi e indice) per creare l'indice.

##Aggiungere campi

In Ricerca di Azure gli attributi di indice, quali searchable, facetable e filterable sono abilitati per impostazione predefinita. In genere, quando si imposta questi attributi, si disattivano i comportamenti di ricerca che non hanno senso (ad esempio, l'ordinamento o esplorazione in base a facet di una descrizione).

Il portale è diverso. Nel portale i comportamenti di ricerca sono disattivati per impostazione predefinita in modo che sia possibile selezionare tutti i comportamenti che si applicano, da campo a campo.

1. Fare clic sull’opzione per l’**aggiunta o la modifica dei campi** per aggiungere ulteriori campi. In questo esercizio, verrà ricreato l’indice degli *hotel* menzionato nell'articolo [Come utilizzare Fiddler con Ricerca di Azure](search-fiddler.md). 

	![][4]

2. Aggiungere e configurare i campi per completare lo schema.

	![][5]

	Vedere le [regole di denominazione](https://msdn.microsoft.com/library/azure/dn857353.aspx) e i [tipi di dati supportati](https://msdn.microsoft.com/library/azure/dn798938.aspx) per informazioni sui nomi di campo e sui tipi di riferimento.

3. Nella parte superiore della pagina fare clic su **Save**.

  	![][6]

##Passaggi successivi

Anche se l'indice è definito, non potrà essere utilizzato fino a quando non si caricano documenti. Per effettuare ciò in modo semplice, continuare con [Come utilizzare Fiddler con Ricerca di Azure](search-fiddler.md) in **Caricare i documenti**. È quindi possibile seguire i passaggi rimanenti dell'articolo per eseguire alcune query.

Una volta acquisita familiarità con l'indice di base, prendere in considerazione l'aggiunta di un analizzatore del linguaggio o componente per il suggerimento per aggiungere supporto multilingue o suggerimenti di completamento automatico. Entrambe le funzionalità vengono specificate nello schema di indice. Vedere [Supporto linguistico](https://msdn.microsoft.com/elibrary/azure/dn879793.aspx) e [Creare un indice](https://msdn.microsoft.com/library/azure/dn798941.aspx) per ulteriori informazioni.

<!--Image references-->
[1]: ./media/search-create-index-portal/AzureSearch-PortalIndex-1.PNG
[2]: ./media/search-create-index-portal/AzureSearch-PortalIndex-2.PNG
[3]: ./media/search-create-index-portal/AzureSearch-PortalIndex-3.PNG
[4]: ./media/search-create-index-portal/AzureSearch-PortalIndex-4.PNG
[5]: ./media/search-create-index-portal/AzureSearch-PortalIndex-5.PNG
[6]: ./media/search-create-index-portal/AzureSearch-PortalIndex-6.PNG
<!--HONumber=54--> 