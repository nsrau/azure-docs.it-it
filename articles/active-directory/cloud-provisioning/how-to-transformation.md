---
title: Azure AD Connect le trasformazioni del provisioning cloud
description: In questo documento viene descritto come utilizzare le trasformazioni per modificare i mapping degli attributi predefiniti.
author: billmath
ms.author: billmath
manager: davba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: 0d37fdb4ad0d385914aecd4ca62be498c5c0e7c5
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794471"
---
# <a name="transformations"></a>Trasformazioni

Una trasformazione consente di modificare il comportamento predefinito del modo in cui un attributo viene sincronizzato con Azure AD usando il provisioning cloud.  

Per eseguire questa operazione, è necessario modificare lo schema e quindi inviarlo nuovamente tramite una richiesta Web.

Per ulteriori informazioni sugli attributi di provisioning nel cloud, vedere [informazioni sullo schema di Azure ad](concept-attributes.md).


## <a name="retrieve-the-schema"></a>Recuperare lo schema
Per recuperare lo schema, attenersi alla procedura illustrata in [visualizzazione dello schema](concept-attributes.md#viewing-the-schema). 


## <a name="custom-attribute-mapping"></a>Mapping di attributi personalizzati
Per aggiungere un mapping di attributi personalizzato, attenersi alla procedura riportata di seguito.

1. Copiare lo schema in un editor di testo o di codice, ad esempio [Visual Studio Code](https://code.visualstudio.com/).  
2. Individuare l'oggetto che si desidera aggiornare nello schema ![](media/how-to-transformation/transform1.png)</br>
3. Individuare il codice per **ExtensionAttribute3** nell'oggetto utente.

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
 4.  Modificare il codice in modo che venga eseguito il mapping dell'attributo Company a ExtensionAttribute3.
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
 5. Copiare di nuovo lo schema in Graph Explorer, modificare il tipo di richiesta in PUT ed **eseguire la query**.  
 ![](media/how-to-transformation/transform2.png)</br>
 6.  A questo punto, nella portale di Azure passare alla configurazione del provisioning del cloud e **riavviare il provisioning**.
 ![](media/how-to-transformation/transform3.png)</br>
 7.  Dopo un po' di tempo, verificare che gli attributi vengano popolati eseguendo la query seguente in Graph Explorer: `https://graph.microsoft.com/beta/users/{Azure AD user UPN}`.
 8.  A questo punto verrà visualizzato il valore.
 ![](media/how-to-transformation/transform4.png)</br>

## <a name="custom-attribute-mapping-with-function"></a>Mapping di attributi personalizzati con function
Per un mapping più avanzato, è possibile usare funzioni che consentono di modificare i dati e creare valori per l'attributo in base alle esigenze dell'organizzazione.

Per eseguire questa operazione, è sufficiente seguire la procedura precedente e quindi modificare la funzione usata per costruire il valore finale.

Per informazioni sulla sintassi ed esempi di espressioni, vedere [scrittura di espressioni per i mapping degli attributi in Azure Active Directory](reference-expressions.md)


## <a name="next-steps"></a>Passaggi successivi 

- [Che cos'è il provisioning?](what-is-provisioning.md)
- [Che cos'è Azure AD Connect provisioning cloud?](what-is-cloud-provisioning.md)
