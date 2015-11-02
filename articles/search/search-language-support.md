<properties
   pageTitle="Creare una definizione di indice per la documentazione in più lingue in Ricerca di Azure | Microsoft Azure"
   description="Ricerca di Azure supporta 56 lingue, grazie ai vantaggi offerti dagli analizzatori delle lingue della tecnologia Lucene e di elaborazione del linguaggio naturale di Microsoft."
   services="search"
   documentationCenter=""
   authors="yahnoosh"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="na"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="10/19/2015"
   ms.author="jlembicz"/>

# Creare una definizione di indice per la documentazione in più lingue in Ricerca di Azure

> [AZURE.SELECTOR]
- [REST](https://msdn.microsoft.com/library/azure/dn879793.aspx)
- [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.analyzername.aspx)

Consentire l'espressione della potenza degli analizzatori delle lingue è semplice come l'impostazione di una proprietà in un campo di ricerca nella definizione dell'indice. Questo passaggio può ora essere eseguito nel portale.

Di seguito sono riportate schermate dei pannelli del portale di Azure relativi a Ricerca di Azure che consentono agli utenti di definire uno schema di indice. Da questi pannelli, gli utenti possono creare tutti i campi e impostare la proprietà analyzer per ognuno di essi.

> [AZURE.NOTE]È possibile impostare solo un analizzatore della lingua durante la definizione del campo, come quando si crea un nuovo indice da zero o si aggiunge un nuovo campo a un indice esistente. Assicurarsi di specificare completamente tutti gli attributi, incluso l'analizzatore, durante la creazione del campo. Non sarà possibile modificare gli attributi o cambiare il tipo di analizzatore dopo aver definito il campo.

1. Accedere al [portale di Azure](https://portal.azure.com) e aprire il pannello del servizio di ricerca.
2. Fare clic su **Aggiungi un indice** nella parte superiore del dashboard del servizio per avviare un nuovo indice oppure aprire un indice esistente per impostare un analizzatore nei nuovi campi aggiunti a un indice esistente.
3. Viene visualizzato il pannello Campi, con opzioni per la definizione dello schema dell'indice, inclusa la scheda Analizzatore usata per la scelta di un analizzatore della lingua.
4. In Campi avviare una definizione di campo fornendo un nome, scegliendo il tipo di dati e impostando gli attributi per contrassegnare il campo come disponibile per la ricerca full-text, recuperabile nei risultati della ricerca, utilizzabile nelle strutture di navigazione facet, ordinabile e così via. 
5. Prima di passare al campo successivo, aprire la scheda **Analizzatore**. 
6. Scorrere per trovare il campo che si sta definendo. 
7. Se il campo non è stato contrassegnato per la ricerca, fare clic sulla casella di controllo per contrassegnarlo come Ricercabile.
8. Fare clic sull'area dell'analizzatore per visualizzare l'elenco degli analizzatori disponibili.
9. Scegliere l'analizzatore che si desidera usare.

![][1] *Per selezionare un analizzatore, fare clic sulla scheda Analizzatore nel pannello Campi*

![][2] *Selezionare uno degli analizzatori supportati per ogni campo*

Per impostazione predefinita, tutti i campi di ricerca usano l'[analizzatore Standard Lucene](http://lucene.apache.org/core/4_10_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) che è indipendente dalla lingua. Per visualizzare l'elenco completo degli analizzatori supportati, vedere il post di blog relativo alle [lingue supportate in Ricerca di Azure](https://msdn.microsoft.com/library/azure/dn879793.aspx).

Dopo aver selezionato l'analizzatore della lingua per un campo, verrà usato per ogni richiesta di ricerca e indicizzazione relativa a quel campo. Quando viene eseguita una query su più campi usando analizzatori diversi, la query verrà elaborata in modo indipendente dagli analizzatori specifici di ciascun campo.

Molte applicazioni Web e per dispositivi mobili vengono sfruttate dagli utenti in tutto il mondo in diverse lingue. È possibile definire un indice per uno scenario simile al seguente mediante la creazione di un campo per ogni lingua supportata.

![][3] *Definizione di indice con un campo di descrizione per ogni lingua supportata*

Se la lingua dell'agente che esegue una query è nota, è possibile definire per una richiesta di ricerca un ambito relativo a un campo specifico usando il parametro di query **searchFields**. La query seguente verrà generata solo per la descrizione in polacco:

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=description_pl&api-version=2015-02-28`

È possibile che la lingua dell'agente che esegue una query non sia nota, in tal caso la query può essere inviata a tutti i campi contemporaneamente. Se necessario, è possibile definire delle preferenze per i risultati in una determinata lingua usando i [profili di punteggio](https://msdn.microsoft.com/library/azure/dn798928.aspx). Nell'esempio seguente, alle corrispondenze trovate nella descrizione in inglese viene assegnato un punteggio più elevato rispetto alle corrispondenze in polacco e francese:

    "scoringProfiles": [
      {
        "name": "englishFirst",
        "text": {
          "weights": { "description_en": 2 }
        }
      }
    ]

`https://[service name].search.windows.net/indexes/[index name]/docs?search=Microsoft&scoringProfile=englishFirst&api-version=2015-02-28`

Per gli sviluppatori .NET, si noti che è possibile configurare analizzatori delle lingue mediante l'[SDK .NET di Ricerca di Azure](http://www.nuget.org/packages/Microsoft.Azure.Search/0.13.0-preview). La versione più recente supporta anche gli analizzatori delle lingue di Microsoft.

<!-- Image References -->
[1]: ./media/search-language-support/AnalyzerTab.png
[2]: ./media/search-language-support/SelectAnalyzer.png
[3]: ./media/search-language-support/IndexDefinition.png

<!---HONumber=Oct15_HO4-->