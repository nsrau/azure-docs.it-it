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
ms.topic: article
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a639b14c9313179816f6376aa0c5642a645ea344
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56180826"
---
# <a name="troubleshoot-an-attribute-not-synchronizing-in-azure-ad-connect"></a>Risolvere i problemi relativi a un attributo che non si sincronizza in Azure AD Connect

## <a name="recommended-steps"></a>**Procedure consigliate**

Prima di esaminare i problemi di sincronizzazione di un attributo, è importante conoscere il processo di sincronizzazione di **Azure AD Connect**:

  ![Processo di sincronizzazione di Azure AD Connect](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**Terminologia**

* **CS:** spazio connettore, una tabella nel database.
* **MV:** metaverse, una tabella nel database.
* **AD:** Active Directory
* **AAD:** Azure Active Directory

### <a name="synchronization-steps"></a>**Passaggi di sincronizzazione**

* Importazione da Active Directory: gli oggetti Active Directory vengono importati nello spazio connettore di Active Directory.

* Importazione da Azure Active Directory: gli oggetti Azure Active Directory vengono importati nello spazio connettore di Azure Active Directory.

* Sincronizzazione: vengono eseguite le **regole di sincronizzazione in ingresso** e le regole di sincronizzazione in uscita nell'ordine di numero di precedenza dal più basso al più alto. Per visualizzare le regole di sincronizzazione, è possibile passare all'**editor delle regole di sincronizzazione** dalle applicazioni desktop. Le **regole di sincronizzazione in ingresso** importano dati dallo spazio connettore nel metaverse. Le **regole di sincronizzazione in uscita** spostano i dati dal metaverse allo spazio connettore.

* Esportazione in Active Directory: dopo aver eseguito la sincronizzazione, gli oggetti vengono esportati dallo spazio connettore di Active Directory in **Active Directory**.

* Esportazione in Azure Active Directory: dopo aver eseguito la sincronizzazione, gli oggetti vengono esportati dallo spazio connettore di Azure Active Directory in **Azure Active Directory**.

### <a name="step-by-step-investigation"></a>**Analisi dettagliata**

* Si inizierà la ricerca dal **metaverse** e si esaminerà il mapping degli attributi dall'origine alla destinazione.

* Avviare **Synchronization Service Manager** dalle applicazioni desktop, come mostrato di seguito:

  ![Avviare Synchronization Service Manager](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/startmenu.png)

* In **Synchronization Service Manager** selezionare **Metaverse Search** (Ricerca metaverse), **Scope by Object Type** (Ambito per tipo di oggetto), l'oggetto che usa un attributo e quindi il pulsante **Search** (Cerca).

  ![Metaverse Search (Ricerca metaverse)](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvsearch.png)

* Fare doppio clic sull'oggetto trovato in **Metaverse Search** (Ricerca metaverse) per visualizzare tutti i relativi attributi. È possibile fare clic sulla scheda **Connectors** (Connettori) per esaminare l'oggetto corrispondente in tutti gli **spazi connettore**.

  ![Connettori dell'oggetto metaverse](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvattributes.png)

* Fare doppio clic su **Active Directory Connector** per visualizzare gli attributi dello **spazio connettore**. Fare clic sul pulsante **Preview** (Anteprima) nella finestra di dialogo seguente e quindi sul pulsante **Generate Preview** (Genera anteprima).

  ![Attributi dello spazio connettore](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/csattributes.png)

* A questo punto fare clic su **Import Attribute Flow** (Importa flusso attributi). Verrà visualizzato un flusso di attributi dallo **spazio Active Directory Connector** al **metaverse**. La colonna **Sync Rule** (Regola sincronizzazione) mostra quale **regola di sincronizzazione** ha partecipato per l'attributo. La colonna **Data Source** (Origine dati) mostra gli attributi dello **spazio connettore**. La colonna **Metaverse Attribute** (Attributo metaverse) mostra gli attributi nel **metaverse**. È possibile cercare qui l'attributo non sincronizzato. Se la ricerca ha esito negativo, significa che non è stato eseguito il mapping dell'attributo ed è necessario creare una nuova **regola di sincronizzazione** personalizzata per eseguire il mapping dell'attributo.

  ![Attributi dello spazio connettore](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/cstomvattributeflow.png)

* Fare clic su **Export Attribute Flow** (Esporta flusso attributi) nel riquadro sinistro per visualizzare il flusso di attributi dal **metaverse** allo **spazio Active Directory Connector** con **regole di sincronizzazione in uscita**.

  ![Attributi dello spazio connettore](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvtocsattributeflow.png)

* Analogamente, è possibile visualizzare l'oggetto **spazio connettore Azure Active Directory**  e generare l'**anteprima** per visualizzare il flusso di attributi dal **metaverse** allo **spazio connettore** e viceversa. In questo modo è possibile analizzare perché un attributo non si sincronizza.

## <a name="recommended-documents"></a>**Documenti consigliati**
* [Servizio di sincronizzazione Azure AD Connect: concetti tecnici](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-technical-concepts)
* [Servizio di sincronizzazione Azure AD Connect: informazioni sull'architettura](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-architecture)
* [Servizio di sincronizzazione Azure AD Connect: Informazioni sul provisioning dichiarativo](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning)
* [Servizio di sincronizzazione Azure AD Connect: informazioni sulle espressioni di provisioning dichiarativo](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning-expressions)
* [Servizio di sincronizzazione Azure AD Connect: informazioni sulla configurazione predefinita](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-default-configuration)
* [Servizio di sincronizzazione Azure AD Connect: informazioni su utenti, gruppi e contatti](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-user-and-contacts)
* [Servizio di sincronizzazione Azure AD Connect: attributi shadow](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-syncservice-shadow-attributes)

## <a name="next-steps"></a>Passaggi successivi

- [Servizio di sincronizzazione Azure AD Connect](how-to-connect-sync-whatis.md).
- [Informazioni sull'identità ibrida](whatis-hybrid-identity.md).
