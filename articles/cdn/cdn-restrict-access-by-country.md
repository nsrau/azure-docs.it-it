---
title: Limitare il contenuto della rete CDN di Azure in base al paese | Documentazione Microsoft
description: Informazioni su come limitare l'accesso al contenuto della rete CDN di Azure con la funzionalità di filtro geografico.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: 12c17cc5-28ee-4b0b-ba22-2266be2e786a
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: v-deasim
ms.openlocfilehash: 93321c4c8a7f8d79835d702ca07132eed94f6493
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/11/2018
ms.locfileid: "35260753"
---
# <a name="restrict-azure-cdn-content-by-country"></a>Limitare il contenuto della rete CDN di Azure in base al paese

## <a name="overview"></a>Panoramica
Quando un utente richiede il contenuto, per impostazione predefinita il contenuto viene servito indipendentemente dalla località dell'utente che effettua la richiesta. In alcuni casi, è possibile limitare l'accesso al contenuto in base al paese. Questo articolo illustra come usare la funzionalità di *filtro geografico* per configurare il servizio in modo da consentire o bloccare l'accesso in base al paese.

> [!IMPORTANT]
> I prodotti per la rete CDN di Azure offrono la stessa funzionalità di filtro geografico, ma con una lieve differenza a livello di indicativi paese supportati. Per altre informazioni, vedere [Azure CDN Country Codes](https://msdn.microsoft.com/library/mt761717.aspx) (Codici paese per la rete CDN di Azure).


Per informazioni sulle considerazioni relative alla configurazione di questo tipo di restrizione, vedere [Considerazioni](cdn-restrict-access-by-country.md#considerations).  

![filtro di paese](./media/cdn-filtering/cdn-country-filtering-akamai.png)

## <a name="step-1-define-the-directory-path"></a>Passaggio 1: definire il percorso di directory
> [!IMPORTANT]
> I profili di **rete CDN Standard di Azure con tecnologia Microsoft** non supportano il filtro geografico basato sul percorso.
>


Per trovare questa funzionalità, selezionare l'endpoint all'interno del portale e individuare la scheda Filtro geografico nel riquadro di spostamento a sinistra.

Quando si configura un filtro di paese, è necessario specificare il percorso relativo della posizione a cui gli utenti potranno o meno accedere. È possibile applicare il filtro geografico a tutti i file con una barra (/) o alle cartelle selezionate specificando i percorsi di directory */pictures/*. È anche possibile applicare il filtro geografico a un singolo file specificando il file ed escludendo la barra finale */pictures/city.png*.

Esempio di filtro di percorso di directory:

    /                                 
    /Photos/
    /Photos/Strasbourg/
      /Photos/Strasbourg/city.png

## <a name="step-2-define-the-action-block-or-allow"></a>Passaggio 2: definire l'azione Blocca o Consenti
**Blocca** : agli utenti dei paesi specificati verrà negato l'accesso alle risorse richieste da quel percorso ricorsivo. Se non sono state definite altre opzioni di filtro di paese per tale percorso, tutti gli altri utenti saranno autorizzati ad accedere.

**Consenti** : solo agli utenti dei paesi specificati verrà autorizzato l'accesso alle risorse richieste da quel percorso ricorsivo.

## <a name="step-3-define-the-countries"></a>Passaggio 3: definire i paesi
Selezionare i paesi che si desidera bloccare o consentire per il percorso. 

Per esempio, la regola di blocco /Photos/Strasbourg/ filtrerà i file tra cui:

    http://<endpoint>.azureedge.net/Photos/Strasbourg/1000.jpg
    http://<endpoint>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg


### <a name="country-codes"></a>Codici paese
La funzionalità di filtro geografico usa i codici paese per definire i paesi da cui una richiesta viene consentita o bloccata per una directory protetta. Anche se tutti i prodotti di rete CDN di Azure offrono la stessa funzionalità di filtro geografico, esiste una lieve differenza a livello di codici paese supportati. Per altre informazioni, vedere [Azure CDN Country Codes](https://msdn.microsoft.com/library/mt761717.aspx) (Codici paese per la rete CDN di Azure). 

## <a name="considerations"></a>Considerazioni
* Le modifiche alla configurazione del filtro paese non diventano immediatamente effettive:
   * La propagazione dei profili della **rete CDN Standard di Azure con tecnologia Microsoft** viene in genere completata in 10 minuti. 
   * La propagazione dei profili di **rete CDN Standard di Azure con tecnologia Akamai** viene in genere completata entro un minuto. 
   * La propagazione dei profili della **rete CDN Standard di Azure con tecnologia Verizon** e della **rete CDN Premium di Azure con tecnologia Verizon** viene in genere completata in 10 minuti. 
 
* Questa funzionalità non supporta i caratteri jolly (ad esempio, ‘*’).

* La configurazione del filtro geografico associata al percorso relativo viene applicata in modo ricorsivo a tale percorso.

* È possibile applicare una sola regola allo stesso percorso relativo. Vale a dire, non è possibile creare più filtri paese che puntano allo stesso percorso relativo. Tuttavia, una cartella può avere più filtri paese, a causa della natura ricorsiva dei filtri paese. In altre parole, una sottocartella di una cartella configurata in precedenza può essere assegnata a un filtro di paese diverso.

