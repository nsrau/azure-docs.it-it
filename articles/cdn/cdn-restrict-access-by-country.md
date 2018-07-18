---
title: Limitare il contenuto della rete CDN di Azure in base al paese | Documentazione Microsoft
description: Informazioni su come limitare l'accesso al contenuto della rete CDN di Azure in base al paese con la funzionalità di filtro geografico.
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
ms.date: 06/19/2018
ms.author: v-deasim
ms.openlocfilehash: 661356aeb2369bc1bbddd6caee57b256dd9e1212
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2018
ms.locfileid: "36285016"
---
# <a name="restrict-azure-cdn-content-by-country"></a>Limitare il contenuto della rete CDN di Azure in base al paese

## <a name="overview"></a>Panoramica
Quando un utente richiede il contenuto, per impostazione predefinita il contenuto viene servito indipendentemente dalla località dell'utente che effettua la richiesta. In alcuni casi, tuttavia, è possibile limitare l'accesso al contenuto in base al paese. Con la funzionalità di *filtro geografico* è possibile creare regole in percorsi specifici nell'endpoint della rete CDN per consentire o bloccare il contenuto in determinati paesi.

> [!IMPORTANT]
> I profili di **rete CDN Standard di Azure con tecnologia Microsoft** non supportano il filtro geografico basato sul percorso.
> 

## <a name="standard-profiles"></a>Profili standard
Le procedure in questa sezione si applicano solo ai profili per la **rete CDN Standard di Azure fornita da Akamai** e la **rete CDN Standard di Azure fornita da Verizon**. 

Per i profili della **rete CDN Premium di Azure fornita da Verizon** è necessario usare il portale **Gestione** per attivare il filtro geografico. Per altre informazioni, vedere [Profili della rete CDN Premium di Azure fornita da Verizon](#azure-cdn-premium-from-verizon-profiles).

### <a name="define-the-directory-path"></a>Definire il percorso di directory
Per accedere alla funzionalità di filtro geografico, selezionare l'endpoint della rete CDN all'interno del portale e quindi selezionare **Filtro geografico** in Impostazioni nel menu a sinistra. 

![Filtro geografico standard](./media/cdn-filtering/cdn-geo-filtering-standard.png)

Nella casella **Percorso** specificare il percorso relativo della posizione a cui gli utenti potranno o meno accedere. 

È possibile applicare il filtro geografico a tutti i file con una barra (/) o selezionare cartelle specifiche indicando i percorsi di directory, ad esempio */foto/*. È anche possibile applicare il filtro geografico a un singolo file (ad esempio */foto/città.png*). Sono consentite più regole. Dopo aver immesso una regola, viene visualizzata una riga vuota per immettere la regola successiva.

Ad esempio, tutti i filtri di percorso di directory seguenti sono validi:   
*/*                                 
*/Foto/*     
*/Foto/Strasburgo/*     
*/Foto/Strasburgo/città.png*

### <a name="define-the-type-of-action"></a>Definire il tipo di azione

Nell'elenco **Azione** selezionare **Consenti** o **Blocca**: 

- **Consenti**: solo gli utenti dei paesi specificati sono autorizzati ad accedere alle risorse richieste dal percorso ricorsivo.

- **Blocca**: agli utenti dei paesi specificati viene negato l'accesso alle risorse richieste dal percorso ricorsivo. Se non sono state definite altre opzioni di filtro di paese per tale percorso, tutti gli altri utenti saranno autorizzati ad accedere.

Ad esempio, una regola di filtro geografico per bloccare il percorso */Foto/Strasburgo/* filtra i file seguenti:     
*http://<endpoint>.azureedge.net/Foto/Strasburgo/1000.jpg*
*http://<endpoint>.azureedge.net/Foto/Strasburgo/Cattedrale/1000.jpg*

### <a name="define-the-countries"></a>Definire i paesi
Nell'elenco **Codici paese** selezionare i paesi che si vuole bloccare o consentire per il percorso. 

Dopo aver selezionato i paesi, selezionare **Salva** per attivare la nuova regola di filtro geografico. 

![Regole di filtro geografico](./media/cdn-filtering/cdn-geo-filtering-rules.png)

### <a name="clean-up-resources"></a>Pulire le risorse
Per eliminare una regola, selezionarla nell'elenco nella pagina **Filtro geografico** e quindi scegliere **Elimina**.

## <a name="azure-cdn-premium-from-verizon-profiles"></a>Profili della rete CDN Premium di Azure fornita da Verizon
Per i profili della **rete CDN Premium di Azure fornita da Verizon**, l'interfaccia utente per la creazione di una regola di filtro geografico è diversa:

1. Nel menu principale nel profilo della rete CDN di Azure selezionare **Gestisci**.

2. Dal portale di Verizon selezionare **HTTP Large** (HTTP esteso) e quindi selezionare **Country Filtering** (Filtro paese).

    ![Filtro geografico standard](./media/cdn-filtering/cdn-geo-filtering-premium.png)

3. Selezionare **Add Country Filter** (Aggiungi filtro paese).

    Verrà visualizzata la pagina **Step One:** (Passaggio 1).

4. Immettere il percorso della directory, selezionare **Block** (Blocca) oppure **Add** (Aggiungi) e quindi selezionare **Next** (Avanti).

    Verrà visualizzata la pagina **Step Two:** (Passaggio 2). 

5. Selezionare uno o più paesi nell'elenco e quindi selezionare **Fine** per attivare la regola. 
    
    La nuova regola viene visualizzata nella tabella nella pagina **Country Filtering** (Filtro paese).

    ![Regole di filtro geografico](./media/cdn-filtering/cdn-geo-filtering-premium-rules.png)

### <a name="clean-up-resources"></a>Pulire le risorse
Nella tabella delle regole di filtro del paese, selezionare l'icona di eliminazione accanto a una regola per eliminarla o sull'icona di modifica per modificarla.

## <a name="considerations"></a>Considerazioni
* Le modifiche alla configurazione del filtro geografico non diventano immediatamente effettive:
   * La propagazione dei profili della **rete CDN Standard di Azure con tecnologia Microsoft** viene in genere completata in 10 minuti. 
   * La propagazione dei profili di **rete CDN Standard di Azure con tecnologia Akamai** viene in genere completata entro un minuto. 
   * La propagazione dei profili della **rete CDN Standard di Azure con tecnologia Verizon** e della **rete CDN Premium di Azure con tecnologia Verizon** viene in genere completata in 10 minuti. 
 
* Questa funzionalità non supporta i caratteri jolly (ad esempio, *).

* La configurazione del filtro geografico associata al percorso relativo viene applicata in modo ricorsivo a tale percorso.

* È possibile applicare una sola regola allo stesso percorso relativo. Vale a dire, non è possibile creare più filtri paese che puntano allo stesso percorso relativo. Tuttavia, a causa della natura ricorsiva dei filtri paese, una cartella può avere più filtri paese. In altre parole, una sottocartella di una cartella configurata in precedenza può essere assegnata a un filtro di paese diverso.

* La funzionalità di filtro geografico usa i codici paese per definire i paesi da cui una richiesta viene consentita o bloccata per una directory protetta. Anche se i profili Akamai e Verizon supportano la maggior parte degli stessi codici paese, esistono alcune differenze. Per altre informazioni, vedere [Azure CDN country codes](https://msdn.microsoft.com/library/mt761717.aspx) (Codici paese per la rete CDN di Azure). 

