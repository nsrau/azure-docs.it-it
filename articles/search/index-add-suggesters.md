---
title: Aggiungere strumenti suggerimenti a un indice di Ricerca di Azure
description: Abilita i campi per le azioni di completamento automatico delle query, in cui le query suggerite sono costituite da testo proveniente da campi di un indice di Ricerca di Azure.
ms.date: 01/31/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 35025d69865aa6890e1cd921e31ac6c26c015789
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2019
ms.locfileid: "56007699"
---
# <a name="add-suggesters-to-an-azure-search-index"></a>Aggiungere strumenti suggerimenti a un indice di Ricerca di Azure

Uno **Strumento suggerimenti** è un costrutto di Ricerca di Azure che supporta la funzionalità per i [Suggerimenti](https://docs.microsoft.com/rest/api/searchservice/suggestions) "search-as-you-type" (ricerca durante la digitazione) e la funzionalità di [completamento automatico (anteprima)](search-autocomplete-tutorial.md). Prima di poter chiamare l'API per i suggerimenti, è necessario definire uno **strumento suggerimenti** in un indice per abilitare i suggerimenti in campi specifici.

Anche se uno **strumento suggerimenti** ha diverse proprietà, è principalmente una raccolta di campi per il quale si sta abilitando l'[API per i suggerimenti](https://docs.microsoft.com/rest/api/searchservice/suggestions). Per un'app di viaggio, ad esempio, potrebbe essere necessario abilitare la ricerca con completamento automatico su destinazioni, città e attrazioni. Di conseguenza, tutti e tre i campi finirebbero nella raccolta di campi.

Si può avere un solo **strumento suggerimenti** per ogni indice, in particolare uno **strumento suggerimenti** nella raccolta **strumento suggerimenti**.

È possibile creare uno **strumento suggerimenti** in qualsiasi momento, ma l'impatto sull'indice varia in base ai campi. I nuovi campi aggiunti a uno strumento suggerimenti come parte dello stesso aggiornamento sono i meno impattanti, in quanto non è richiesta la ricompilazione di un indice. L'aggiunta di campi esistenti, tuttavia, cambia la definizione di un campo, per cui è necessario eseguire una ricompilazione completa dell'indice.

## <a name="usage"></a>Uso  

 Gli **strumenti suggerimenti** funzionano in modo ottimale quando vengono usati per suggerire documenti specifici anziché espressioni o termini separati. I campi che rappresentano i candidati migliori sono i titoli, i nomi e le altre frasi relativamente brevi che possono identificare un elemento. I campi meno efficaci sono quelli ripetitivi, come le categorie e i tag, o quelli molto lunghi, come le descrizioni o i commenti.  

Dopo aver creato uno strumento suggerimenti, aggiungere l'[API per i suggerimenti](https://docs.microsoft.com/rest/api/searchservice/suggestions) nella logica di query per richiamare la funzionalità.  

Le proprietà che definiscono lo **strumento suggerimenti** sono le seguenti:  

|Proprietà|DESCRIZIONE|  
|--------------|-----------------|  
|`name`|Nome dello **strumento suggerimenti**. Si usa il nome dello **strumento suggerimenti** quando si chiama [Suggestions &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/suggestions) (Suggerimenti: API REST per il servizio di Ricerca di Azure).|  
|`searchMode`|La strategia usata per la ricerca di espressioni candidate. L'unica modalità attualmente supportata è `analyzingInfixMatching`, che ricerca una corrispondenza flessibile di espressioni all'inizio o all'interno di frasi.|  
|`sourceFields`|Un elenco di uno o più campi che sono l'origine del contenuto per i suggerimenti. Solo i campi di tipo `Edm.String` e `Collection(Edm.String)` possono essere origini per i suggerimenti. È possibile usare solo campi per i quali non è impostato un analizzatore di lingua personalizzato. |  

## <a name="suggester-example"></a>Esempio di strumento suggerimenti  
 Uno **strumento suggerimenti** fa parte della definizione dell'indice. Nella versione attuale della raccolta **strumenti suggerimenti** può essere presente un solo **strumento suggerimenti**, insieme alla raccolta dei **campi** e degli **scoringProfiles**.  

```  
{  
  "name": "hotels",  
  "fields": [  
     . . .   
   ],  
  "suggesters": [  
    {  
    "name": "sg",  
    "searchMode": "analyzingInfixMatching",  
    "sourceFields": ["hotelName", "category"]  
    }  
  ],  
  "scoringProfiles": [  
     . . .   
  ]  
}  

```  

## <a name="see-also"></a>Vedere anche   
 [Create Index &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  (Creare l'indice: API REST per il servizio Ricerca di Azure)  
 [Update Index &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/update-index)  (Aggiornare l'indice: API REST per il servizio Ricerca di Azure)  
 [Suggestions &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/suggestions)  (Suggerimenti: API REST per il servizio Ricerca di Azure)  
 [Index operations &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/index-operations)  (Operazioni sugli indici API REST per il servizio Ricerca di Azure)  
 [Azure Search Service REST](https://docs.microsoft.com/rest/api/searchservice/)  (REST per il servizio Ricerca di Azure)  
 [Ricerca di Azure .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)  
