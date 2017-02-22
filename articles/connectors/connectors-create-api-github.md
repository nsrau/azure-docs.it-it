---
title: GitHub | Documentazione Microsoft
description: "Creare app per la logica in Servizio app di Azure. GitHub è un servizio di hosting di repository Git basato sul Web. Offre tutte le funzionalità di Git per il controllo delle revisioni distribuite e la gestione del codice sorgente , oltre a diverse funzionalità aggiuntive."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 8f873e6c-f4c0-4c2e-a5bd-2e953efe5e2b
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: 6f336db8c8719b99420b353dca82e36a0d837769


---
# <a name="get-started-with-the-github-connector"></a>Introduzione al connettore GitHub
GitHub è un servizio di hosting di repository Git basato sul Web. Offre tutte le funzionalità di Git per il controllo delle revisioni distribuite e la gestione del codice sorgente , oltre a diverse funzionalità aggiuntive.

> [!NOTE]
> Questa versione dell'articolo si applica alla versione schema 2015-08-01 di anteprima delle app per la logica. 
> 
> 

Per iniziare subito a creare un'app per la logica, vedere [Creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Trigger e azioni
Il connettore GitHub può essere usato come azione e include trigger. Tutti i connettori supportano dati nei formati JSON e XML. 

 Nel connettore GitHub sono disponibili le azioni e/o i trigger seguenti:

### <a name="github-actions"></a>Azioni di GitHub
È possibile eseguire queste azioni:

| Azione | Descrizione |
| --- | --- |
| [CreateIssue](connectors-create-api-github.md#createissue) |Crea un problema |

### <a name="github-triggers"></a>Trigger di GitHub
È possibile ascoltare questi eventi:

| Trigger | Descrizione |
| --- | --- |
| Quando viene aperto un problema |Viene aperto un problema |
| Quando viene chiuso un problema |Viene chiuso un problema |
| Quando viene assegnato un problema |Viene assegnato un problema |

## <a name="create-a-connection-to-github"></a>Creare una connessione a GitHub
Per creare app per la logica con GitHub, è prima necessario creare una **connessione** e quindi indicare i dettagli per le proprietà seguenti: 

| Proprietà | Obbligatorio | Descrizione |
| --- | --- | --- |
| Token |Sì |Fornisce le credenziali per GitHub |

Dopo aver creato la connessione, è possibile usarla per eseguire le azioni e restare in ascolto dei trigger descritti in questo articolo. 

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 
> [!TIP]
> È possibile usare questa connessione in altre app per la logica.
> 
> 

## <a name="reference-for-github"></a>Informazioni di riferimento per GitHub
Si applica alla versione: 1.0

## <a name="createissue"></a>CreateIssue
Creare un problema: crea un problema 

```POST: /repos/{repositoryOwner}/{repositoryName}/issues``` 

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| repositoryOwner |string |Sì |path |nessuno |Proprietario del repository |
| repositoryName |stringa |Sì |path |nessuno |Nome del repository |
| issueBasicDetails | |sì |body |nessuno |Dettagli del problema |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita. |

## <a name="issueopened"></a>IssueOpened
Quando viene aperto un problema: viene aperto un problema 

```GET: /trigger/issueOpened``` 

Non sono disponibili parametri per questa chiamata

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita. |

## <a name="issueclosed"></a>IssueClosed
Quando viene chiuso un problema: viene chiuso un problema 

```GET: /trigger/issueClosed``` 

Non sono disponibili parametri per questa chiamata

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita. |

## <a name="issueassigned"></a>IssueAssigned
Quando viene assegnato un problema: viene assegnato un problema 

```GET: /trigger/issueAssigned``` 

Non sono disponibili parametri per questa chiamata

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita. |

## <a name="object-definitions"></a>Definizioni oggetti
### <a name="issuebasicdetailsmodel"></a>IssueBasicDetailsModel
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| title |stringa |Sì |
| body |stringa |Sì |
| assignee |stringa |Sì |

### <a name="issuedetailsmodel"></a>IssueDetailsModel
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| title |stringa |Sì |
| body |stringa |Sì |
| assignee |stringa |Sì |
| number |string |No |
| state |stringa |No |
| created_at |stringa |No |
| repository_url |stringa |No |

## <a name="next-steps"></a>Passaggi successivi
[Creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md)




<!--HONumber=Feb17_HO3-->


