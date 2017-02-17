---
title: Limitare il contenuto della rete CDN di Azure in base al paese | Documentazione Microsoft
description: "Informazioni su come limitare l&quot;accesso al contenuto della rete CDN di Azure con la funzionalità Filtro geografico."
services: cdn
documentationcenter: 
author: lichard
manager: akucer
editor: 
ms.assetid: 12c17cc5-28ee-4b0b-ba22-2266be2e786a
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
translationtype: Human Translation
ms.sourcegitcommit: dccb945e170bd3e3f23283359db25e574a2d4296
ms.openlocfilehash: 30160088d9c770400f342e67527e1cf1cabc4f6b


---
# <a name="restrict-azure-cdn-content-by-country"></a>Limitare il contenuto della rete CDN di Azure in base al paese

## <a name="overview"></a>Panoramica
Quando un utente richiede il contenuto, per impostazione predefinita il contenuto viene servito indipendentemente dalla località dell'utente che effettua la richiesta. In alcuni casi, è possibile limitare l'accesso al contenuto in base al paese. Questo argomento illustra come usare la funzionalità **Filtro geografico** per configurare il servizio in modo da consentire o bloccare l'accesso in base al paese.

> [!IMPORTANT]
> I prodotti Verizon e Akamai forniscono la stessa funzionalità di filtro geografico, ma con una lieve differenza a livello di indicativi paese supportati. Per un collegamento relativo alle differenze, vedere il Passaggio 3.


Per informazioni su considerazioni relative alla configurazione di questo tipo di restrizioni, vedere la sezione [Considerazioni](cdn-restrict-access-by-country.md#considerations) alla fine dell'argomento.  

![filtro di paese](./media/cdn-filtering/cdn-country-filtering-akamai.png)

## <a name="step-1-define-the-directory-path"></a>Passaggio 1: definire il percorso di directory
Per trovare questa funzionalità, selezionare l'endpoint all'interno del portale e individuare la scheda Filtro geografico nel riquadro di spostamento a sinistra.

Quando si configura un filtro di paese, è necessario specificare il percorso relativo della posizione a cui gli utenti potranno o meno accedere. È possibile applicare il filtro geografico a tutti i file con "/" o alle cartelle selezionate specificando i percorsi di directory "/pictures/". È inoltre possibile applicare il filtro geografico a un singolo file specificando il file ed escludendo la barra finale "/pictures/city.png".

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
La funzionalità **Filtro geografico** usa i codici paese per definire i paesi da cui una richiesta viene consentita o bloccata per una directory protetta. Gli indicativi paese sono disponibili nella pagina [Azure CDN Country Codes](https://msdn.microsoft.com/library/mt761717.aspx) (Indicativi paese della rete CDN di Azure). 

## <a name="a-idconsiderationsaconsiderations"></a><a id="considerations"></a>Considerazioni
* Per rendere effettive le modifiche alla configurazione del filtro geografico possono essere necessari fino a 90 minuti per Verizon o un paio di minuti con Akamai.
* Questa funzionalità non supporta i caratteri jolly (ad esempio, ‘*’).
* La configurazione del filtro geografico associata al percorso relativo viene applicata in modo ricorsivo a tale percorso.
* Può essere applicata solo una regola allo stesso percorso relativo (non è possibile creare più filtri di paese che puntano allo stesso percorso relativo). Tuttavia, una cartella potrebbe avere più filtri di paese. Ciò è dovuto alla natura ricorsiva dei filtri di paese. In altre parole, una sottocartella di una cartella configurata in precedenza può essere assegnata a un filtro di paese diverso.




<!--HONumber=Jan17_HO4-->


