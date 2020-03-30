---
title: Trasformazioni del provisioning cloud di Azure AD ConnectAzure AD Connect cloud provisioning transformations
description: In questo articolo viene descritto come utilizzare le trasformazioni per modificare i mapping di attributi predefiniti.
author: billmath
ms.author: billmath
manager: davba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: ec12927b40096b7ff04fae6b7cbc69a7bc11e8f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75549296"
---
# <a name="transformations"></a>Trasformazioni

Con una trasformazione, è possibile modificare il comportamento predefinito della sincronizzazione di un attributo con Azure Active Directory (Azure AD) tramite il provisioning cloud.

Per eseguire questa attività, è necessario modificare lo schema e quindi inviarlo nuovamente tramite una richiesta Web.To do this task, you need to edit the schema and then resubmit it via a web request.

Per altre informazioni sugli attributi di provisioning cloud, vedere Informazioni sullo schema di Azure AD.For more information on cloud provisioning attributes, see [Understanding the Azure AD schema.](concept-attributes.md)


## <a name="retrieve-the-schema"></a>Recuperare lo schema
Per recuperare lo schema, seguire i passaggi descritti in [Visualizzare lo schema](concept-attributes.md#view-the-schema). 

## <a name="custom-attribute-mapping"></a>Mapping degli attributi personalizzati
Per aggiungere un mapping di attributi personalizzato, attenersi alla seguente procedura.

1. Copiare lo schema in un editor di testo o di codice, ad esempio [Visual Studio Code](https://code.visualstudio.com/).
1. Individuare l'oggetto che si desidera aggiornare nello schema.

   ![Oggetto nello schema](media/how-to-transformation/transform1.png)</br>
1. Individuare il `ExtensionAttribute3` codice per sotto l'oggetto utente.

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
1. Modificare il codice in modo che `ExtensionAttribute3`l'attributo company sia mappato a .

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
 1. Copiare nuovamente lo schema in Graph Explorer, modificare il **tipo di richiesta** in **PUT**e selezionare **Esegui query**.

    ![Eseguire la query](media/how-to-transformation/transform2.png)

 1. Ora, nel portale di Azure, passare alla configurazione di provisioning cloud e selezionare **Riavvia provisioning**.

    ![Riavviare il provisioning](media/how-to-transformation/transform3.png)

 1. Dopo un po', verificare che gli attributi vengano `https://graph.microsoft.com/beta/users/{Azure AD user UPN}`popolati eseguendo la query seguente in Graph Explorer: .
 1. Ora dovresti vedere il valore.

    ![Il valore viene visualizzato](media/how-to-transformation/transform4.png)

## <a name="custom-attribute-mapping-with-function"></a>Mapping degli attributi personalizzati con funzione
Per un mapping più avanzato, è possibile utilizzare funzioni che consentono di modificare i dati e creare valori per gli attributi in base alle esigenze dell'organizzazione.

Per eseguire questa operazione, seguire i passaggi precedenti e quindi modificare la funzione utilizzata per costruire il valore finale.

Per informazioni sulla sintassi e sugli esempi di espressioni, vedere Scrittura di espressioni per i mapping degli [attributi in Azure Active Directory.](reference-expressions.md)


## <a name="next-steps"></a>Passaggi successivi 

- [Cos'è il provisioning?](what-is-provisioning.md)
- [Che cos'è il provisioning cloud di Azure AD Connect?](what-is-cloud-provisioning.md)
