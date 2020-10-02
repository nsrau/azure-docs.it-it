---
title: Editor attributi di provisioning di Azure AD Connect Cloud
description: Questo articolo descrive come usare l'editor di attributi.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/22/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58b7aa0bf8c82990a00b4e41041145a67ee2f02b
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2020
ms.locfileid: "91637220"
---
# <a name="azure-ad-connect-cloud-provisioning-attibute-mapping"></a>Mapping di attributo per il provisioning del Cloud Azure AD Connect

Azure AD Connect provisioning cloud ha introdotto una nuova funzionalità che consente di eseguire facilmente il mapping degli attributi tra gli oggetti utente/gruppo locali e gli oggetti in Azure AD.  Questa funzionalità è stata aggiunta alla configurazione del provisioning del cloud.

È possibile personalizzare i mapping degli attributi predefiniti in base alle esigenze aziendali. Quindi è possibile modificare o eliminare i mapping degli attributi esistenti oppure crearne di nuovi.  Per un elenco di attributi sincronizzati, vedere [attributi sincronizzati](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md).

## <a name="understanding-attribute-mapping-types"></a>Informazioni sui tipi di mapping degli attributi
Con mapping degli attributi è possibile controllare la modalità di popolamento degli attributi in Azure AD.
Sono supportati quattro diversi tipi di mapping:

- **Direct** : l'attributo di destinazione viene popolato con il valore di un attributo dell'oggetto collegato in Active Directory.
- **Costante:** l'attributo di destinazione viene popolato con una stringa specificata.
- **Espressione:** l'attributo di destinazione viene popolato in base al risultato di un'espressione simile a uno script.
  Per ulteriori informazioni, vedere [scrittura di espressioni per mapping degli attributi](reference-expressions.md).
- **Nessuno:** l'attributo di destinazione viene lasciato invariato. Se l'attributo di destinazione viene lasciato vuoto, viene popolato con il valore predefinito specificato.

Oltre a questi quattro tipi base, i mapping degli attributi personalizzati supportano il concetto di assegnazione di un valore **predefinito** facoltativo. L'assegnazione di un valore predefinito garantisce che un attributo di destinazione venga popolato con un valore nel caso in cui non sia presente un valore in Azure AD o nell'oggetto di destinazione. Nella maggior parte delle configurazioni questo campo viene lasciato vuoto.

## <a name="understanding-attribute-mapping-properties"></a>Informazioni sulle proprietà di mapping degli attributi

Nella sezione precedente è già stata presentata la proprietà del tipo di mapping degli attributi.
Oltre a questa proprietà i mapping degli attributi supportano i seguenti attributi:

- **Attributo di origine** : attributo utente del sistema di origine, ad esempio Active Directory.
- **Attributo di destinazione** : attributo utente nel sistema di destinazione, ad esempio Azure Active Directory.
- **Valore predefinito se Null (facoltativo)** : valore che verrà passato al sistema di destinazione se l'attributo di origine è Null. Il provisioning di questo valore verrà eseguito solo quando viene creato un utente. Il provisioning del "valore predefinito se Null" non verrà eseguito quando si aggiorna un utente esistente.  
- **Applica questo mapping**
  - **Sempre**: applica il mapping sia all'azione di creazione che all'azione di aggiornamento dell'utente.
  - **Solo durante la creazione**: applica il mapping solo alle azioni di creazione dell'utente.

> [!NOTE]
> In questo documento viene descritto come utilizzare il portale di Azure per eseguire il mapping degli attributi.  Per informazioni sull'utilizzo di Graph, vedere [trasformazioni](how-to-transformation.md)

## <a name="using-attribute-mapping"></a>Uso del mapping degli attributi
Per usare la nuova funzionalità, attenersi alla procedura riportata di seguito.

 1.  Nella portale di Azure selezionare **Azure Active Directory**.
 2.  Selezionare **Azure AD Connect**.
 3.  Selezionare **Gestisci provisioning**.

   ![Gestire il provisioning](media/how-to-configure/manage1.png)
 
 4. In **configurazione**selezionare la configurazione.
 5. Selezionare **fare clic per modificare i mapping**.  Verrà visualizzata la schermata mapping attributi.

 ![Aggiunta di attributi](media/how-to-attribute-mapping/mapping6.png)
 6.  Fare clic su **Aggiungi attributo**.

 ![Tipo di mapping](media/how-to-attribute-mapping/mapping1.png)
 
 7. Selezionare il **tipo di mapping**.  In questo esempio viene usata l'espressione.
 8.  Immettere l'espressione nella casella.  Per questo esempio viene usato: `Replace([mail], "@contoso.com", , ,"", ,).`
 9.  Immettere l'attributo di destinazione.  In questo esempio viene usato ExtensionAttribute15.
 10. Selezionare quando applicare questa operazione e quindi fare clic su **applica** .
   
   ![Modifica mapping](media/how-to-attribute-mapping/mapping2a.png)
 11. Tornare alla schermata mapping attributi. verrà visualizzato il nuovo mapping degli attributi.  
 12. Fare clic su **Salva schema**.

 ![Salva schema](media/how-to-attribute-mapping/mapping3.png)

## <a name="test-your-attribute-mapping"></a>Testare il mapping degli attributi
Per testare il mapping degli attributi, è possibile usare [il provisioning su richiesta](how-to-on-demand-provision.md).  Dall' 

1.  Nella portale di Azure selezionare **Azure Active Directory**.
2.  Selezionare **Azure AD Connect**.
3.  Selezionare **Gestisci provisioning**.
4. In **configurazione**selezionare la configurazione.
5. In **convalida** fare clic sul pulsante Esegui il **provisioning di un utente** . 
6. Nella schermata di provisioning su richiesta.  Immettere il **nome distinto** di un utente o di un gruppo e fare clic sul pulsante **provisioning** .  
7. Al termine, verrà visualizzata una schermata operazione completata e 4 caselle di controllo verde che indicano che è stato eseguito correttamente il provisioning.  
  ![Operazione riuscita per il provisioning](media/how-to-attribute-mapping/mapping4.png)
1. In **Esegui azione** fare clic su **Visualizza dettagli**.  A destra dovrebbe essere visualizzato il nuovo attributo syncrhonized e l'espressione applicata.

  ![Azione da eseguire](media/how-to-attribute-mapping/mapping5.png)

## <a name="next-steps"></a>Passaggi successivi

- [Che cos'è il provisioning cloud di Azure AD Connect?](what-is-cloud-provisioning.md)
- [Scrittura di espressioni per i mapping degli attributi](reference-expressions.md)
- [Attributi che vengono sincronizzati](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md)