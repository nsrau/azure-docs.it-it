---
title: Limitare l&quot;accesso al contenuto della rete CDN di Azure in base al paese | Documentazione Microsoft
description: "Informazioni su come limitare l&quot;accesso al contenuto della rete CDN di Azure con la funzionalità Filtro geografico."
services: cdn
documentationcenter: 
author: lichard
manager: akucer
editor: 
ms.assetid: 20943ac8-9dce-4cf2-85ed-e8362750aa63
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2016
ms.author: rli
translationtype: Human Translation
ms.sourcegitcommit: 2ee72c31072fb5201e057f4c3567d33ce53e4fbc
ms.openlocfilehash: 1a1cf8cea1b321bb4e758e9ad06e42da59d3bcb4


---
# <a name="restrict-access-to-your-content-by-country---verizon"></a>Limitare l'accesso al contenuto in base al paese: Verizon
> [!div class="op_single_selector"]
> * [Verizon](cdn-restrict-access-by-country.md)
> * [Akamai Standard](cdn-restrict-access-by-country-akamai.md)
> 
> 

## <a name="overview"></a>Panoramica
Quando un utente richiede il contenuto, per impostazione predefinita il contenuto viene servito indipendentemente dalla località dell'utente che effettua la richiesta. In alcuni casi, è possibile limitare l'accesso al contenuto in base al paese. Questo argomento illustra come usare la funzionalità **Filtro geografico** per configurare il servizio in modo da consentire o bloccare l'accesso in base al paese.

> [!IMPORTANT]
> I prodotti Verizon e Akamai forniscono la stessa funzionalità di filtro geografico, ma con un'interfaccia utente diversa. Questo documento descrive l'interfaccia della **rete CDN Standard di Azure fornita da Verizon** e della **rete CDN Premium di Azure fornita da Verizon**. Per la funzionalità di filtro geografico con la **rete CDN Standard di Azure fornita da Akamai**, vedere [Restrict access to your content by country - Akamai](cdn-restrict-access-by-country-akamai.md) (Limitare l'accesso al contenuto in base al paese: Akamai).
> 
> 

Per informazioni su considerazioni relative alla configurazione di questo tipo di restrizioni, vedere la sezione [Considerazioni](cdn-restrict-access-by-country.md#considerations) alla fine dell'argomento.  

> [!NOTE]
> Una volta impostata la configurazione, verrà applicata a tutti gli endpoint della **rete CDN di Azure fornita da Verizon** nel profilo CDN di Azure.
> 
> 

![filtro di paese](./media/cdn-filtering/cdn-country-filtering.png)

## <a name="step-1-define-the-directory-path"></a>Passaggio 1: definire il percorso di directory
Quando si configura un filtro di paese, è necessario specificare il percorso relativo della posizione a cui gli utenti potranno o meno accedere. È possibile applicare il filtro di paese per tutti i file con "/" o le cartelle selezionate specificando i percorsi di directory.

Esempio di filtro di percorso di directory:

    /                                 
    /Photos/
    /Photos/Strasbourg

## <a name="step-2-define-the-action-block-or-allow"></a>Passaggio 2: definire l'azione Blocca o Consenti
**Blocca** : agli utenti dei paesi specificati verrà negato l'accesso alle risorse richieste da quel percorso ricorsivo. Se non sono state definite altre opzioni di filtro di paese per tale percorso, tutti gli altri utenti saranno autorizzati ad accedere.

**Consenti** : solo agli utenti dei paesi specificati verrà autorizzato l'accesso alle risorse richieste da quel percorso ricorsivo.

## <a name="step-3-define-the-countries"></a>Passaggio 3: definire i paesi
Selezionare i paesi che si desidera bloccare o consentire per il percorso. Per altre informazioni, vedere [Azure CDN from Verizon Country Codes](https://msdn.microsoft.com/library/mt761717.aspx)(Indicativi paese della rete CDN di Azure fornita da Verizon).

Per esempio, la regola di blocco /Photos/Strasbourg/ filtrerà i file tra cui:

    http://<endpoint>.azureedge.net/Photos/Strasbourg/1000.jpg
    http://<endpoint>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg


## <a name="country-codes"></a>Codici paese
La funzionalità **Filtro geografico** usa i codici paese per definire i paesi da cui una richiesta viene consentita o bloccata per una directory protetta. Gli indicativi paese sono disponibili nella pagina [Azure CDN from Verizon Country Codes](https://msdn.microsoft.com/library/mt761717.aspx)(Indicativi paese della rete CDN di Azure fornita da Verizon). Se si specifica "UE" (Europa) o "PA" (Asia/Pacifico), un sottoinsieme di indirizzi IP che provengono da paesi in quelle aree geografiche verrà bloccato o consentito.

## <a name="a-idconsiderationsaconsiderations"></a><a id="considerations"></a>Considerazioni
* Per rendere effettive le modifiche alla configurazione del filtro paese possono essere necessari anche 90 minuti.
* Questa funzionalità non supporta i caratteri jolly (ad esempio, ‘*’).
* La configurazione del filtro di paese associata al percorso relativo viene applicata in modo ricorsivo a tale percorso.
* Può essere applicata solo una regola allo stesso percorso relativo (non è possibile creare più filtri di paese che puntano allo stesso percorso relativo). Tuttavia, una cartella potrebbe avere più filtri di paese. Ciò è dovuto alla natura ricorsiva dei filtri di paese. In altre parole, una sottocartella di una cartella configurata in precedenza può essere assegnata a un filtro di paese diverso.




<!--HONumber=Nov16_HO3-->


