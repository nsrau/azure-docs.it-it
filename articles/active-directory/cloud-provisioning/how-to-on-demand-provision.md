---
title: Provisioning su richiesta di Azure AD Connect cloud provisioning
description: Questo articolo descrive la funzionalità di provisioning su richiesta.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72623d69f9fb0cbc68758a362bb977e5e02d2c00
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91637172"
---
# <a name="azure-ad-connect-cloud-provisioning-on-demand-provisioning"></a>Provisioning su richiesta di Azure AD Connect cloud provisioning

Azure AD Connect provisioning cloud ha introdotto una nuova funzionalità che consente di testare le modifiche alla configurazione, applicando queste modifiche a un singolo utente.  È possibile usarlo per convalidare e verificare che le modifiche apportate alla configurazione siano state applicate correttamente e siano sincronizzate correttamente con Azure AD.  

> [!IMPORTANT] 
> Quando si usa il provisioning su richiesta, i filtri di ambito non vengono applicati all'utente selezionato.  Ciò significa che è possibile usare il provisioning su richiesta sugli utenti esterni alle unità organizzative specificate.


## <a name="using-on-demand-provisioning"></a>Uso del provisioning su richiesta
Per usare la nuova funzionalità, attenersi alla procedura riportata di seguito.


1.  Nella portale di Azure selezionare **Azure Active Directory**.
2.  Selezionare **Azure AD Connect**.
3.  Selezionare **Gestisci provisioning**.

    ![Gestire il provisioning](media/how-to-configure/manage1.png)
4. In **configurazione**selezionare la configurazione.
5. In **convalida** fare clic sul pulsante Esegui il **provisioning di un utente** . 

 ![Effettuare il provisioning di un utente](media/how-to-on-demand-provision/on-demand2.png)

6. Nella schermata di provisioning su richiesta.  Immettere il **nome distinto** di un utente e fare clic sul pulsante **provision (provisioning** ).  
 
 ![Provisioning su richiesta](media/how-to-on-demand-provision/on-demand3.png)
7. Al termine, verrà visualizzata una schermata operazione completata e 4 caselle di controllo verde che indicano che è stato eseguito correttamente il provisioning.  Tutti gli errori verranno visualizzati a sinistra.

  ![Operazione completata](media/how-to-on-demand-provision/on-demand4.png)

A questo punto è possibile esaminare l'utente e determinare se sono state applicate le modifiche apportate alla configurazione.  Nella parte restante di questo documento vengono descritte le singole sezioni visualizzate nei dettagli di un utente sincronizzato correttamente.

## <a name="import-user-details"></a>Importa dettagli utente
In questa sezione vengono fornite informazioni sull'utente importato da Active Directory.  Questo è l'aspetto dell'utente prima che venga eseguito il provisioning in Azure AD.  Fare clic sul collegamento **Visualizza dettagli** per visualizzare queste informazioni.

![Importa utente](media/how-to-on-demand-provision/on-demand5.png)

Utilizzando queste informazioni, è possibile visualizzare i vari attributi e i rispettivi valori importati.  Se è stato creato un mapping di attributi personalizzato, sarà possibile visualizzare il valore qui.
![Importa dettagli utente](media/how-to-on-demand-provision/on-demand6.png)

## <a name="determine-if-user-is-in-scope-details"></a>Determinare se l'utente è nei dettagli dell'ambito
In questa sezione vengono fornite informazioni sul fatto che l'utente importato in Azure AD sia nell'ambito.  Fare clic sul collegamento **Visualizza dettagli** per visualizzare queste informazioni.

![Ambito user](media/how-to-on-demand-provision/on-demand7.png)

Utilizzando queste informazioni, è possibile visualizzare informazioni aggiuntive sull'ambito degli utenti.

![Dettagli ambito utente](media/how-to-on-demand-provision/on-demand10a.png)

## <a name="match-user-between-source-and-target-system-details"></a>Corrisponde all'utente tra i dettagli del sistema di origine e di destinazione
In questa sezione vengono fornite informazioni sul fatto che l'utente esista già nel Azure AD e che venga generato un join anziché eseguire il provisioning di un nuovo utente.  Fare clic sul collegamento **Visualizza dettagli** per visualizzare queste informazioni.
![Visualizzare i dettagli](media/how-to-on-demand-provision/on-demand8.png)

Utilizzando queste informazioni, è possibile verificare se è stata trovata una corrispondenza o se è in corso la creazione di un nuovo utente.

![Informazioni utente](media/how-to-on-demand-provision/on-demand11.png)

Nei dettagli corrispondenti viene visualizzato un messaggio con una delle tre operazioni seguenti.  ovvero:
- Crea: viene creato un utente in Azure AD
- Aggiornamento: un utente viene aggiornato in base a una modifica apportata alla configurazione
- Delete: un utente viene rimosso dal Azure AD.

Il messaggio può variare a seconda del tipo di operazione eseguita.

## <a name="perform-action-details"></a>Esegui dettagli azione
In questa sezione vengono fornite informazioni sull'utente di cui è stato effettuato il provisioning o che è stato esportato in Azure AD dopo l'applicazione della configurazione.  Si tratta di un aspetto simile a quello dell'utente dopo il provisioning in Azure AD.  Fare clic sul collegamento **Visualizza dettagli** per visualizzare queste informazioni.
![Esegui dettagli azione](media/how-to-on-demand-provision/on-demand9.png)

Utilizzando queste informazioni, è possibile visualizzare i valori degli attributi dopo l'applicazione della configurazione.  Hanno un aspetto simile a quello che è stato importato o sono i diversi?  La configurazione si applica correttamente?  

Questo processo consente di tracciare la trasformazione dell'attributo mentre viene spostata nel cloud e nel tenant Azure AD.

![Trace (attributo)](media/how-to-on-demand-provision/on-demand12.png)

## <a name="next-steps"></a>Passaggi successivi 

- [Che cos'è il provisioning cloud di Azure AD Connect?](what-is-cloud-provisioning.md)
- [Come installare Azure AD Connect provisioning cloud](how-to-install.md)
 