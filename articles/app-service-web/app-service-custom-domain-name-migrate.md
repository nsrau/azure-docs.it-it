---
title: Eseguire la migrazione di un dominio personalizzato attivo al Servizio app di Azure | Documentazione Microsoft
description: "Informazioni su come eseguire la migrazione di un dominio personalizzato già assegnato a un sito live sull&quot;app del Servizio app di Azure senza tempi di inattività."
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: jimbe
tags: top-support-issue
ms.assetid: 10da5b8a-1823-41a3-a2ff-a0717c2b5c2d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: d6d506eef720488969c5b33fe4b94c02752c6b58
ms.lasthandoff: 04/03/2017


---
# <a name="migrate-an-active-custom-domain-to-azure-app-service"></a>Eseguire la migrazione di un dominio personalizzato attivo al Servizio app di Azure

Questo articolo descrive come eseguire la migrazione di un dominio personalizzato attivo al [Servizio app di Azure](../app-service/app-service-value-prop-what-is.md) senza tempi di inattività.

Quando si esegue la migrazione di un sito live e del relativo nome di dominio al servizio app, il nome di dominio è già in uso e pertanto non è opportuno che si verifichi alcun tempo di inattività nella risoluzione DNS durante il processo di migrazione. In questo caso, è necessario associare preventivamente il nome di dominio all'app di Azure per la verifica. 

## <a name="prerequisites"></a>Prerequisiti

In questo articolo si presuppone che l'utente conosca già le procedure per [eseguire manualmente il mapping di un dominio personalizzato al servizio app](web-sites-custom-domain-name.md).

## <a name="bind-the-domain-name-preemptively"></a>Associare preventivamente il nome del dominio

Quando si associa preventivamente un dominio personalizzato, è necessario eseguire entrambe le operazioni seguenti prima di apportare modifiche ai record DNS:

- Verificare la proprietà del dominio
- Abilitare il nome di dominio per l'app

Quando infine si modifica il record DNS in modo che punti all'app del servizio app, i client vengono reindirizzati dal vecchio sito all'applicazione del servizio app senza tempi di inattività durante la risoluzione DNS.

Attenersi ai passaggi indicati di seguito:

1. Creare innanzitutto un record TXT di verifica con il registro di sistema DNS seguendo i passaggi descritti in [Passaggio 2. Creare i record DNS](web-sites-custom-domain-name.md#createdns).
Il record TXT aggiuntivo segue la convenzione che esegue il mapping da &lt;*sottodominio*>.&lt;*dominioradice*> a &lt;*nomeapp*>.azurewebsites.net.
Vedere la tabella seguente per alcuni esempi:  
 
    <table cellspacing="0" border="1">
    <tr>
    <th>Esempio di FQDN</th>
    <th>Host TXT</th>
    <th>Valore TXT</th>
    </tr>
    <tr>
    <td>contoso.com (radice)</td>
    <td>awverify.contoso.com</td>
    <td>&lt;<i>appname</i>>.azurewebsites.net</td>
    </tr>
    <tr>
    <td>www.contoso.com (sottodominio)</td>
    <td>awverify.www.contoso.com</td>
    <td>&lt;<i>appname</i>>.azurewebsites.net</td>
    </tr>
    <tr>
    <td>\*.contoso.com (carattere jolly)</td>
    <td>awverify\*.contoso.com</td>
    <td>&lt;<i>appname</i>>.azurewebsites.net</td>
    </tr>
    </table>

2. Aggiungere quindi il nome di dominio personalizzato all'app di Azure seguendo i passaggi descritti in [Passaggio 3. Abilitare il nome di dominio personalizzato per l'app](web-sites-custom-domain-name.md#enable).

    Il dominio personalizzato è abilitato nell'app di Azure. È ora necessario aggiornare il record DNS presso il registrar.

3. Aggiornare infine il record DNS del dominio in modo che punti all'app di Azure, come illustrato in [Passaggio 2. Creare i record DNS](web-sites-custom-domain-name.md#createdns). 

    Il traffico utente verrà reindirizzato all'app di Azure immediatamente dopo la propagazione del DNS.

## <a name="next-steps"></a>Passaggi successivi
Informazioni su come proteggere il nome di dominio personalizzato tramite HTTPS [acquistando un certificato SSL in Azure](web-sites-purchase-ssl-web-site.md) o [usando un certificato SSL per un dominio personalizzato](web-sites-configure-ssl-certificate.md).

> [!NOTE]
> Per iniziare a usare il servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](https://azure.microsoft.com/try/app-service/), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.
> 
> 

[Introduzione a DNS di Azure](../dns/dns-getstarted-create-dnszone.md)  
[Creare record DNS per un'app Web in un dominio personalizzato](../dns/dns-web-sites-custom-domain.md)  
[Delegare un dominio al servizio DNS di Azure](../dns/dns-domain-delegation.md)


