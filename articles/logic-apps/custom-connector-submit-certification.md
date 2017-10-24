---
title: Certificare i connettori personalizzati - App per la logica di Azure | Microsoft Docs
description: Rendere disponibili i connettori per tutti gli utenti in App per la logica di Azure, Microsoft Flow e Microsoft PowerApps
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 9bcb138c181df0c12548cc5dc721542fd9c9ba68
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="submit-your-connectors-for-microsoft-certification"></a>Inviare i connettori per la certificazione di Microsoft

Per rendere disponibile pubblicamente i connettori personalizzati per tutti gli utenti in App per la logica di Azure, Microsoft Flow e Microsoft PowerApps, inviare il connettore a Microsoft per la revisione, la convalida e l'approvazione per la pubblicazione. 

## <a name="certification-criteria"></a>Criteri di certificazione

| Funzionalità | Dettagli | Obbligatori o consigliati |
|------------|---------|-------------------------|
| App Software-as-a-Service (SaaS) | Soddisfa uno scenario dell'utente che si adatta perfettamente con le App per la logica, Flow e PowerApps. | Obbligatorio |
| Tipo di autenticazione | L'API deve supportare OAuth2, la chiave API o l'autenticazione di base. | Obbligatorio | 
| Supporto | È necessario fornire un contatto di supporto in modo che i clienti possano ricevere aiuto. | Obbligatorio | 
| Disponibilità e tempo di attività | L'app ha almeno il 99,9% di tempo di attività. | Consigliato | 
|||| 

Se non si è un partner di Microsoft o un fornitore di Software indipendenti (ISV) e si desidera certificare e rilasciare pubblicamente un connettore, è necessario o essere il proprietario del servizio sottostante o presentare dei diritti espliciti per usare l'API.

## <a name="validation-phases"></a>Fasi di convalida

Microsoft usa queste fasi di convalida per valutare il connettore:

| Fase di convalida | Descrizione | 
| ----- | ----------- |
| Funzionalità | Microsoft esegue il test del connettore con le App per la logica, Flow e PowerApps per gli errori seguenti: <p>- OpenAPI non valido (Swagger) o errori di JSON che vengono visualizzati nella sezione Definizione dalla procedura guidata del connettore personalizzato <p>- Runtime ed errori di convalida dello schema che vengono visualizzati nella sezione Test dalla procedura guidata del connettore personalizzato | 
| Content | Microsoft verifica che il connettore usi nomi e descrizioni ottimizzati per ogni entità. Assicurarsi che ogni operazione, parametro di input e attributo di risposta nel connettore Swagger abbia questi elementi: <p>- [Riepilogo](../logic-apps/custom-connector-openapi-extensions.md#summary) <br>- [Descrizione](../logic-apps/custom-connector-openapi-extensions.md#description) </br>- [Informazioni sulla visibilità](../logic-apps/custom-connector-openapi-extensions.md#visibility) | 
||| 

## <a name="nominate-and-submit-your-connector-to-microsoft-for-certification"></a>Nominare e inviare il connettore a Microsoft per la certificazione

Per richiedere la certificazione, seguire la procedura seguente:

1. **Nominare**

   1. [Inviare la nomina](https://go.microsoft.com/fwlink/?linkid=848754).

      1. Fare clic su **Contattaci** nella parte inferiore della pagina.
      2. Riempire il modulo e selezionare **le domande sul programma ISV Connector e sul co-marketing**.
      3. Scegliere **Invia**.

   2. Firmare l'accordo reciproco di riservatezza e l'accordo di partnership che si riceve. 

      Microsoft richiede questi contratti firmati prima di procedere. 
      È quindi possibile controllare se il connettore soddisfa i criteri di certificazione. 
   
   3. Se il connettore viene approvato, Microsoft invia una notifica con le istruzioni per il caricamento.
    
2. **Revisione**

   1. Inviare queste informazioni al contatto della nomina per la revisione:

      * Il file OpenAPI che descrive l'API
      * Il file dell'icona (con estensione png o jpg) che rappresenta il connettore 
      
        L'icona deve disporre di un logo da ~160 pixel all'interno di un quadrato da 230 pixel. 
        È preferibile un logo bianco su sfondo colorato.
      
      * Il colore della marca dell'icona in formato esadecimale, che deve corrispondere allo sfondo colorato nel file dell'icona

      * Un account di prova per la convalida
      * Un contatto di supporto

   2. Se saranno necessarie altre informazioni, si procederà a contattare.

3. **Pubblica**

    Dopo la convalida della funzionalità e del contenuto del connettore, si ospita il connettore per la distribuzione in tutti i prodotti e in tutte le aree.
    
    Per impostazione predefinita, tutti i connettori vengono pubblicati come connettori "premium". 
    Se è stata compilata l'app con Azure, è possibile richiedere di elencare il connettore come connettore "standard" disponibile per tutti gli utenti con i piani Enterprise di Office 365. 
    Per altri dettagli, chiedere al contatto della nomina.

## <a name="next-steps"></a>Passaggi successivi

* [Domande frequenti sul connettore personalizzato](../logic-apps/custom-connector-faq.md)
* [Panoramica del connettore personalizzato](../logic-apps/custom-connector-overview.md)