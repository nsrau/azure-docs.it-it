---
title: Risolvere i problemi relativi a un attributo che non si sincronizza in Azure AD Connect | Microsoft Docs
description: Questo argomento spiega come risolvere i problemi relativi alla sincronizzazione di un attributo tramite l'attività di risoluzione dei problemi.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6df1347eab57a6971fe2e39c0a55869c8f23939
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91317488"
---
# <a name="troubleshoot-an-attribute-not-synchronizing-in-azure-ad-connect"></a>Risolvere i problemi relativi a un attributo che non si sincronizza in Azure AD Connect

## <a name="recommended-steps"></a>**Procedura consigliata**

Prima di esaminare i problemi di sincronizzazione di un attributo, è importante conoscere il processo di sincronizzazione di **Azure AD Connect**:

  ![Processo di sincronizzazione di Azure AD Connect](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**Terminologia**

* **CS:** Spazio connettore, una tabella nel database.
* **MV:** Metaverse, una tabella nel database.
* **Ad:** Active Directory
* **AAD:** Azure Active Directory

### <a name="synchronization-steps"></a>**Passaggi di sincronizzazione**

* Importa da AD: gli oggetti Active Directory vengono introdotti in Servizi certificati Active Directory.

* Importa da AAD: gli oggetti Azure Active Directory vengono introdotti in AAD CS.

* Sincronizzazione: le regole di sincronizzazione in **ingresso** e **le regole di sincronizzazione** in uscita vengono eseguite nell'ordine di numero di precedenza da inferiore a superiore. Per visualizzare le regole di sincronizzazione, è possibile passare all'**editor delle regole di sincronizzazione** dalle applicazioni desktop. Le **regole di sincronizzazione in ingresso** importano dati dallo spazio connettore nel metaverse. Le **regole di sincronizzazione in uscita** spostano i dati dal metaverse allo spazio connettore.

* Esporta in Active Directory: dopo l'esecuzione della sincronizzazione, gli oggetti vengono esportati da Servizi certificati Active Directory al **Active Directory**.

* Esporta in AAD: dopo l'esecuzione della sincronizzazione, gli oggetti vengono esportati da AAD CS a **Azure Active Directory**.

### <a name="step-by-step-investigation"></a>**Analisi dettagliata**

* Si inizierà la ricerca dal **metaverse** e si esaminerà il mapping degli attributi dall'origine alla destinazione.

* Avviare **Synchronization Service Manager** dalle applicazioni desktop, come mostrato di seguito:

  ![Avviare Synchronization Service Manager](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/startmenu.png)

* In **Synchronization Service Manager** selezionare **Metaverse Search** (Ricerca metaverse), **Scope by Object Type** (Ambito per tipo di oggetto), l'oggetto che usa un attributo e quindi il pulsante **Search** (Cerca).

  ![Metaverse Search (Ricerca metaverse)](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvsearch.png)

* Fare doppio clic sull'oggetto trovato in **Metaverse Search** (Ricerca metaverse) per visualizzare tutti i relativi attributi. È possibile fare clic sulla scheda **Connectors** (Connettori) per esaminare l'oggetto corrispondente in tutti gli **spazi connettore**.

  ![Connettori dell'oggetto metaverse](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvattributes.png)

* Fare doppio clic su **Active Directory Connector** per visualizzare gli attributi dello **spazio connettore**. Fare clic sul pulsante **Preview** (Anteprima) nella finestra di dialogo seguente e quindi sul pulsante **Generate Preview** (Genera anteprima).

  ![Screenshot che mostra la schermata delle proprietà dell'oggetto spazio connettore con il pulsante Anteprima evidenziato.](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/csattributes.png)

* A questo punto fare clic su **Import Attribute Flow** (Importa flusso attributi). Verrà visualizzato un flusso di attributi dallo **spazio Active Directory Connector** al **metaverse**. La colonna **Sync Rule** (Regola sincronizzazione) mostra quale **regola di sincronizzazione** ha partecipato per l'attributo. La colonna **Data Source** (Origine dati) mostra gli attributi dello **spazio connettore**. La colonna **Metaverse Attribute** (Attributo metaverse) mostra gli attributi nel **metaverse**. È possibile cercare qui l'attributo non sincronizzato. Se la ricerca ha esito negativo, significa che non è stato eseguito il mapping dell'attributo ed è necessario creare una nuova **regola di sincronizzazione** personalizzata per eseguire il mapping dell'attributo.

  ![Attributi dello spazio connettore](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/cstomvattributeflow.png)

* Fare clic su **Export Attribute Flow** (Esporta flusso attributi) nel riquadro sinistro per visualizzare il flusso di attributi dal **metaverse** allo **spazio Active Directory Connector** con **regole di sincronizzazione in uscita**.

  ![Screenshot che mostra il flusso dell'attributo dal metaverse allo spazio Active Directory connettore usando le regole di sincronizzazione in uscita.](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvtocsattributeflow.png)

* Analogamente, è possibile visualizzare l'oggetto **spazio connettore Azure Active Directory ** e generare l'**anteprima** per visualizzare il flusso di attributi dal **metaverse** allo **spazio connettore** e viceversa. In questo modo è possibile analizzare perché un attributo non si sincronizza.

## <a name="recommended-documents"></a>**Documenti consigliati**
* [Azure AD Connect Sync: concetti tecnici](./how-to-connect-sync-technical-concepts.md)
* [Servizio di sincronizzazione Azure AD Connect: informazioni sull'architettura](./concept-azure-ad-connect-sync-architecture.md)
* [Servizio di sincronizzazione Azure AD Connect: Informazioni sul provisioning dichiarativo](./concept-azure-ad-connect-sync-declarative-provisioning.md)
* [Azure AD Connect Sync: informazioni sulle espressioni di provisioning dichiarativo](./concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)
* [Servizio di sincronizzazione Azure AD Connect: Informazioni sulla configurazione predefinita](./concept-azure-ad-connect-sync-default-configuration.md)
* [Servizio di sincronizzazione Azure AD Connect: Informazioni su utenti, gruppi e contatti](./concept-azure-ad-connect-sync-user-and-contacts.md)
* [Azure AD Connect Sync: attributi Shadow](./how-to-connect-syncservice-shadow-attributes.md)

## <a name="next-steps"></a>Passaggi successivi

- [Azure ad Connect sincronizzazione](how-to-connect-sync-whatis.md).
- [Che cos'è l'identità ibrida?](whatis-hybrid-identity.md)