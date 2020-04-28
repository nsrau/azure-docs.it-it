---
title: Azure AD Connect le trasformazioni del provisioning cloud
description: Questo articolo descrive come usare le trasformazioni per modificare i mapping degli attributi predefiniti.
author: billmath
ms.author: billmath
manager: davba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: ec12927b40096b7ff04fae6b7cbc69a7bc11e8f6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75549296"
---
# <a name="transformations"></a>Trasformazioni

Con una trasformazione è possibile modificare il comportamento predefinito del modo in cui un attributo viene sincronizzato con Azure Active Directory (Azure AD) usando il provisioning cloud.

Per eseguire questa operazione, è necessario modificare lo schema e quindi inviarlo nuovamente tramite una richiesta Web.

Per ulteriori informazioni sugli attributi di provisioning nel cloud, vedere [informazioni sullo schema di Azure ad](concept-attributes.md).


## <a name="retrieve-the-schema"></a>Recuperare lo schema
Per recuperare lo schema, attenersi alla procedura descritta in [visualizzare lo schema](concept-attributes.md#view-the-schema). 

## <a name="custom-attribute-mapping"></a>Mapping di attributi personalizzati
Per aggiungere un mapping di attributi personalizzato, attenersi alla seguente procedura.

1. Copiare lo schema in un editor di testo o di codice, ad esempio [Visual Studio Code](https://code.visualstudio.com/).
1. Individuare l'oggetto che si desidera aggiornare nello schema.

   ![Oggetto nello schema](media/how-to-transformation/transform1.png)</br>
1. Individuare il codice per `ExtensionAttribute3` sotto l'oggetto utente.

    ```
                            {
                                "defaultValue": null,
                                "exportMissingReferences": false,
                                "flowBehavior": "FlowWhenChanged",
                                "flowType": "Always",
                                "matchingPriority": 0,
                                "targetAttributeName": "ExtensionAttribute3",
                                "source": {
                                    "expression": "Trim([extensionAttribute3])",
                                    "name": "Trim",
                                    "type": "Function",
                                    "parameters": [
                                        {
                                            "key": "source",
                                            "value": {
                                                "expression": "[extensionAttribute3]",
                                                "name": "extensionAttribute3",
                                                "type": "Attribute",
                                                "parameters": []
                                            }
                                        }
                                    ]
                                }
                            },
    ```
1. Modificare il codice in modo che venga eseguito il mapping dell' `ExtensionAttribute3`attributo Company a.

   ```
                                    {
                                        "defaultValue": null,
                                        "exportMissingReferences": false,
                                        "flowBehavior": "FlowWhenChanged",
                                        "flowType": "Always",
                                        "matchingPriority": 0,
                                        "targetAttributeName": "ExtensionAttribute3",
                                        "source": {
                                            "expression": "Trim([company])",
                                            "name": "Trim",
                                            "type": "Function",
                                            "parameters": [
                                                {
                                                    "key": "source",
                                                    "value": {
                                                        "expression": "[company]",
                                                        "name": "company",
                                                        "type": "Attribute",
                                                        "parameters": []
                                                    }
                                                }
                                            ]
                                        }
                                    },
   ```
 1. Copiare di nuovo lo schema in Graph Explorer, modificare il **tipo di richiesta** in **put**e selezionare **Esegui query**.

    ![Eseguire la query](media/how-to-transformation/transform2.png)

 1. A questo punto, nel portale di Azure, passare alla configurazione del provisioning del cloud e selezionare **Riavvia provisioning**.

    ![Riavviare il provisioning](media/how-to-transformation/transform3.png)

 1. Dopo un po' di tempo, verificare che gli attributi vengano popolati eseguendo la query seguente in Graph Explorer `https://graph.microsoft.com/beta/users/{Azure AD user UPN}`:.
 1. A questo punto verrà visualizzato il valore.

    ![Viene visualizzato il valore](media/how-to-transformation/transform4.png)

## <a name="custom-attribute-mapping-with-function"></a>Mapping di attributi personalizzati con function
Per un mapping più avanzato, è possibile usare funzioni che consentono di modificare i dati e creare valori per gli attributi in base alle esigenze dell'organizzazione.

Per eseguire questa operazione, seguire i passaggi precedenti e quindi modificare la funzione usata per costruire il valore finale.

Per informazioni sulla sintassi e sugli esempi di espressioni, vedere [scrittura di espressioni per i mapping degli attributi in Azure Active Directory](reference-expressions.md).


## <a name="next-steps"></a>Passaggi successivi 

- [Cos'è il provisioning?](what-is-provisioning.md)
- [Che cos'è il provisioning cloud di Azure AD Connect?](what-is-cloud-provisioning.md)
