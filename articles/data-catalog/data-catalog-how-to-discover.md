<properties
   pageTitle="Come trovare le origini dati | Microsoft Azure"
   description="Articolo sulle procedure di individuazione degli asset di dati registrati con il catalogo dati di Azure, che include ricerca e filtri e l’utilizzo della funzionalità di evidenziazione dei risultati del portale catalogo dati di Azure."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="07/12/2016"
   ms.author="maroche"/>

# Come individuare le origini dati

## Introduzione
**Catalogo dati di Microsoft Azure** è un servizio cloud completamente gestito che funge da sistema di registrazione e di individuazione per origini dati aziendali. In altre parole, il **Catalogo dati di Azure** consente agli utenti di individuare, comprendere e usare origini dati e aiuta le organizzazioni a ottenere maggior valore dai dati esistenti. Una volta che un’origine dati è stata registrata con il**Catalogo dati Azure**, i relativi metadati vengono indicizzati dal servizio, in modo che gli utenti possano eseguire facilmente ricerche per individuare i dati necessari.

## Ricerca e filtri

L’individuazione delle origini dati in **Catalogo dati Azure** utilizza due meccanismi principali: ricerca e filtri.

La ricerca è progettata per essere intuitiva e potente: per impostazione predefinita, i termini di ricerca vengono confrontati con qualsiasi proprietà del catalogo, tra cui le annotazioni fornite dall'utente.

I filtri sono progettati per completare la ricerca. Gli utenti possono selezionare caratteristiche specifiche, ad esempio esperti, tipo di origine dati, tipo di oggetto e tag, per visualizzare solo gli asset di dati corrispondenti e per limitare anche i risultati della ricerca ai corrispondenti asset.

Utilizzando una combinazione di ricerca e filtri, gli utenti possono navigare rapidamente tra le origini dati che sono state registrate con il **Catalogo dati Azure** per individuare quelle di proprio interesse.

## Sintassi di ricerca

Anche se la ricerca di testo libero predefinita è semplice e intuitiva, gli utenti possono utilizzare anche la sintassi di ricerca del**Catalogo dati Azure**per ottenere un maggiore controllo sui risultati della ricerca. La ricerca di **Catalogo dati azure** supporta le seguenti tecniche:

| Tecnica | Uso | Esempio |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------|
| Ricerca di base | Ricerca di base utilizzando uno o più termini di ricerca. I risultati sono gli asset che corrispondono alle proprietà di uno o più termini specificati. | dati di vendita |
| Ambito della proprietà | Restituire solo le origini di dati dove il termine di ricerca corrisponde alla proprietà specificata | nome:finanza |
| Operatori booleani | Ampliare o restringere una ricerca utilizzando operazioni booleane | finanza NON aziendale |
| Raggruppamento con parentesi | Utilizzare le parentesi per raggruppare parti della query per ottenere l'isolamento logico, soprattutto in combinazione con gli operatori booleani | nanome:finanza E (tag:Q1 O tag:Q2) |
| Operatori di confronto | Utilizzare confronti invece di uguaglianze per le proprietà che hanno dati di tipo numero e data | modifiedTime > "11/05/2014" |

Per ulteriori informazioni sulla ricerca del **Catalogo dati Azure**, vedere [https://msdn.microsoft.com/library/azure/mt267594.aspx](https://msdn.microsoft.com/library/azure/mt267594.aspx).

## Evidenziazione dei risultati
Quando si visualizzano i risultati della ricerca, le proprietà visualizzate che corrispondono ai criteri di ricerca specificati, ad esempio nome di asset di dati, descrizione e tag, verranno evidenziate per rendere più semplice identificare il motivo per cui un asset di dati specificato è stato restituito da una ricerca specifica.

> [AZURE.NOTE] Gli utenti possono disattivare l’evidenziazione dei risultati se desiderano, tramite l'opzione "Evidenziazione" nel portale del **Catalogo dati Azure**.

Quando si visualizzano i risultati della ricerca, il motivo per cui un asset di dati è incluso potrebbe non essere sempre evidente, anche con l’evidenziazione abilitata. Poiché tutte le proprietà vengono ricercate per impostazione predefinita, un asset di dati potrebbe essere restituito a causa di una corrispondenza con una proprietà a livello di colonna. E poiché più utenti possono annotare gli asset di dati registrati con tag e descrizioni, è possibile che non tutti i metadati vengano visualizzati nell'elenco dei risultati della ricerca.

Nella visualizzazione affiancata predefinita, ogni riquadro mostrato nei risultati della ricerca includerà un'icona "visualizza le corrispondenze con il termine di ricerca", che consente all'utente di visualizzare rapidamente il numero di corrispondenze e il relativo percorso e di passare a esse se lo si desidera.

 ![Evidenziazione dei risultati e corrispondenze di ricerca nel portale catalogo dati di Azure.](./media/data-catalog-how-to-discover/search-matches.png)

## Riepilogo
La registrazione di un'origine dati con il **Catalogo dati di Azure** rende più semplice individuare e comprendere l'origine dati, copiando i metadati strutturali e descrittivi dall'origine dati nel servizio Catalogo. Una volta registrata un'origine dati, gli utenti possono individuarla mediante i filtri e la ricerca all'interno del portale **Catalogo dati Azure**.

## Vedere anche
- Per altre informazioni dettagliate sull'individuazione delle origini dati, vedere [Introduzione al Catalogo dati di Azure](data-catalog-get-started.md).

<!---HONumber=AcomDC_0713_2016-->