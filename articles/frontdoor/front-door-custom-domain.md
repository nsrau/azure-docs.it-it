---
title: Esercitazione - Aggiungere un dominio personalizzato alla configurazione della Frontdoor di Azure | Microsoft Doc
description: In questa esercitazione, si apprenderà come eseguire l'onboarding a un dominio personalizzato per la Frontdoor di Azure.
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 58829bcd1b3c38b70929167beae5d8866483d616
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2018
ms.locfileid: "53716498"
---
# <a name="tutorial-add-a-custom-domain-to-your-front-door"></a>Esercitazione: Aggiungere un dominio personalizzato alla frontdoor
Questa esercitazione mostra come aggiungere un dominio personalizzato alla propria frontdoor. Quando si usa il servizio Frontdoor di Azure per la distribuzione dell'applicazione, un dominio personalizzato è necessario se si vuole che il nome di dominio sia visibile nella richiesta dell'utente finale. Avere un nome di dominio visibile può essere pratico per i clienti e utile a scopo di personalizzazione.

Dopo aver creato una frondoor, l'host di front-end predefinito, ovvero un sottodominio di `azurefd.net`, è incluso nell'URL per la distribuzione dei contenuti frontdoor dal back-end per impostazione predefinita (ad esempio, https:\//contoso.azurefd.net/activeusers.htm). Per comodità, Frontdoor di Azure offre la possibilità di associare un dominio personalizzato all'host predefinito. Grazie a questa possibilità, si distribuisce il contenuto con un dominio personalizzato nell'URL invece che con un nome di dominio di proprietà di Frontdoor (ad esempio, https:\//www.contoso.com/photo.png). 

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> - Creare un record DNS CNAME.
> - Associare il dominio personalizzato alla propria frontdoor.
> - Verificare il dominio personalizzato.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Prima di poter completare i passaggi di questa esercitazione, è necessario creare una frontdoor. Per altre informazioni, vedere [Guida introduttiva: Creare una frontdoor](quickstart-create-front-door.md).

Se non si dispone già di un dominio personalizzato, è prima necessario acquistarne uno con un provider di dominio. Ad esempio, vedere [Acquistare un nome di dominio personalizzato](https://docs.microsoft.com/azure/app-service/manage-custom-dns-buy-domain).

Se si usa Azure per ospitare i [domini DNS](https://docs.microsoft.com/azure/dns/dns-overview), è necessario delegare il DNS (Domain Name System) del provider di dominio a un DNS di Azure. Per altre informazioni, vedere [Delegare un dominio a DNS di Azure](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns). In caso contrario, se si usa un provider di dominio per gestire il dominio DNS, continuare con [Creare un record DNS CNAME](#create-a-cname-dns-record).


## <a name="create-a-cname-dns-record"></a>Creare un record DNS CNAME

Prima di poter usare un dominio personalizzato con la propria frontdoor, è necessario creare un record di nome canonico (CNAME) con il provider del dominio per puntare all'endpoint della rete CDN all'host di front-end predefinito della frontdoor (ad esempio contose.azurefd.net). Un record CNAME è un tipo di record DNS che esegue il mapping di un nome di dominio di origine a uno di destinazione. Per il servizio Frontdoor di Azure, il nome di dominio di origine è il nome di dominio personalizzato, mentre il nome di dominio di destinazione è il nome host predefinito della frontdoor. Dopo che la frontdoor di Azure ha verificato il record CNAME creato, il traffico indirizzato al dominio personalizzato di origine (ad esempio, www.contoso.com) viene instradato all'host di front-end predefinito della frontdoor di destinazione specificato (ad esempio, contoso.azureedge.net). 

Un dominio personalizzato e il relativo sottodominio possono essere associati a una frontdoor alla volta. È tuttavia possibile usare più sottodomini dello stesso dominio personalizzato per frontdoor diverse usando più record CNAME. È anche possibile eseguire il mapping di un dominio personalizzato con sottodomini diversi alla stessa frontdoor.


## <a name="map-the-temporary-afdverify-sub-domain"></a>Eseguire il mapping del sottodominio afdverify temporaneo

Quando si esegue il mapping di un dominio esistente in fase di produzione, è necessario tenere presenti alcune considerazioni speciali. Mentre si sta registrando il dominio personalizzato nel portale di Azure, può verificarsi un breve tempo di inattività per il dominio. Per evitare l'interruzione del traffico Web, eseguire prima il mapping del dominio personalizzato all'host di front-end predefinito della propria frontdoor con il sottodominio afdverify di Azure per creare un mapping CNAME temporaneo. Con questo metodo, gli utenti possono accedere al dominio senza interruzioni durante l'esecuzione del mapping DNS.

In caso contrario, se si usa per la prima volta il dominio personalizzato in cui non è in esecuzione traffico di produzione, è possibile eseguire il mapping diretto del dominio personalizzato alla frontdoor. Continuare con [Eseguire il mapping del dominio personalizzato permanente](#map-the-permanent-custom-domain).

Per creare un record CNAME con il sottodominio afdverify:

1. Accedere al sito Web del provider del dominio personalizzato.

2. Cercare la pagina per la gestione dei record DNS facendo riferimento alla documentazione del provider oppure eseguendo una ricerca delle aree del sito Web con etichetta come **Nome del dominio**, **DNS** o **Gestione dei server dei nomi**. 

3. Creare una voce di record CNAME per il dominio personalizzato e completare i campi come illustrato nella tabella seguente (i nomi dei campi possono variare):

    | Origine                    | Tipo  | Destinazione                     |
    |---------------------------|-------|---------------------------------|
    | afdverify.www.contoso.com | CNAME | afdverify.contoso.azurefd.NET |

    - Origine: immettere il nome di dominio personalizzato, incluso il sottodominio afdverify, nel formato seguente: afdverify._&lt;nome di dominio personalizzato&gt;_. Ad esempio, afdverify.www.contoso.com.

    - Tipo: immettere *CNAME*.

    - Destinazione: immettere l'host di frontend della frontdoor predefinito, incluso il sottodominio afdverify, nel seguente formato: afdverify._&lt;nome endpoint&gt;_.azurefd.net. Ad esempio, afdverify.contoso.azurefd.net.

4. Salvare le modifiche.

La procedura per il registrar GoDaddy, ad esempio, è la seguente:

1. Accedere al dominio personalizzato da usare e selezionarlo.

2. Nella sezione Domains (Domini) selezionare **Manage All** (Gestisci tutto), quindi selezionare **DNS** | **Manage Zones** (DNS | Gestisci zone).

3. In **Domain Name** (Nome di dominio) immettere il proprio nome di dominio, quindi selezionare **Search** (Cerca).

4. Nella pagina **DNS Management** (Gestione DNS) selezionare **Add** (Aggiungi), quindi selezionare **CNAME** nell'elenco **Type** (Tipo).

5. Completare i campi seguenti della voce CNAME:

    - Type (Tipo): lasciare selezionato *CNAME*.

    - Host: immettere il sottodominio del dominio personalizzato da usare, incluso il nome del sottodominio afdverify. Ad esempio, afdverify.www.

    - Points to (Punta a): immettere il nome host dell'host di front-end della frontdoor predefinito, incluso il nome di sottodominio afdverify. Ad esempio, afdverify.contoso.azurefd.net. 

    - TTL: lasciare selezionato *1 Hour* (1 ora).

6. Selezionare **Salva**.
 
    La voce CNAME viene aggiunta alla tabella di record DNS.


## <a name="associate-the-custom-domain-with-your-front-door"></a>Associare il dominio personalizzato alla propria frontdoor

Dopo avere registrato il dominio personalizzato, è possibile aggiungerlo alla frontdoor.

1. Accedere al [portale di Azure](https://portal.azure.com/) e passare alla frontdoor contenente l'host di front-end di cui si vuole eseguire il mapping a un dominio personalizzato.
    
2. Nella pagina **Finestra di progettazione frontdoor**, fare clic su '+' per aggiungere un dominio personalizzato.
    
3. Specificare un **dominio personalizzato**. 

4. Per **Host di front-end**, l'host di front-end da utilizzare come dominio di destinazione del record CNAME è pre-compilato e deriva da Front Door: *&lt;default hostname&gt;*.azurefd.net. Non può essere modificato.

5. Per **Nome host personalizzato**, immettere il dominio personalizzato, incluso il sottodominio, da usare come dominio di origine del record CNAME. Ad esempio, www.contoso.com o cdn.contoso.com. Non usare il nome del sottodominio afdverify.

6. Selezionare **Aggiungi**.

   Azure verifica l'esistenza del record CNAME per il nome di dominio personalizzato immesso. Se il record CNAME è corretto, il dominio personalizzato verrà convalidato.

>[!WARNING]
> È **necessario** assicurarsi che ognuno degli host front-end nella frontdoor (inclusi i domini personalizzati) disponga di una regola di gestione con un percorso predefinito ('/\*') associato. Questo vuol dire che tra tutte le regole di gestione deve esisterne almeno una per ognuno degli host front-end definita nel percorso predefinito ('/\*'). In caso contrario, è possibile che il traffico degli utenti finali non venga instradato in modo corretto.

## <a name="verify-the-custom-domain"></a>Verificare il dominio personalizzato

Dopo aver completato la registrazione del dominio personalizzato, verificare che il dominio personalizzato faccia riferimento all'host di front-end della frontdoor predefinito.
 
Nel browser passare all'indirizzo del file usando il dominio personalizzato. Se il dominio personalizzato è robotics.contoso.com, ad esempio, l'URL del file memorizzato nella cache sarà simile al seguente: http:\//robotics.contoso.com/my-public-container/my-file.jpg. Verificare che il risultato è lo stesso come quando si accede alla frontdoor direttamente alla *&lt;frontdoor host&gt;*.azurefd.net.


## <a name="map-the-permanent-custom-domain"></a>Eseguire il mapping del dominio personalizzato permanente

Se si è verificato che il mapping del sottodominio afdverify alla frontdoor è stato eseguito correttamente (o se si usa un nuovo dominio personalizzato non in produzione), è possibile eseguire il mapping del dominio personalizzato direttamente all'host di front-end della frontdoor predefinito.

Per creare un record CNAME per il dominio personalizzato:

1. Accedere al sito Web del provider del dominio personalizzato.

2. Cercare la pagina per la gestione dei record DNS facendo riferimento alla documentazione del provider oppure eseguendo una ricerca delle aree del sito Web con etichetta come **Nome del dominio**, **DNS** o **Gestione dei server dei nomi**. 

3. Creare una voce di record CNAME per il dominio personalizzato e completare i campi come illustrato nella tabella seguente (i nomi dei campi possono variare):

    | Origine          | Tipo  | Destinazione           |
    |-----------------|-------|-----------------------|
    | www.contoso.com | CNAME | contoso.azurefd.net |

    - Origine: immettere il nome di dominio personalizzato (ad esempio, www.contoso.com).

    - Tipo: immettere *CNAME*.

    - Destinazione: immettere l'host di front-end della frontdoor predefinito. Deve essere nel formato seguente:_&lt;nome host&gt;_.azurefd.net. Ad esempio, contoso.azurefd.net.

4. Salvare le modifiche.

5. Se si è creato in precedenza un record CNAME del sottodominio afdverify temporaneo, eliminarlo. 

6. Se si usa questo dominio personalizzato nell'ambiente di produzione per la prima volta, seguire i passaggi illustrati in [Associare il dominio personalizzato alla frontdoor](#associate-the-custom-domain-with-your-front-door) e [Verificare il dominio personalizzato](#verify-the-custom-domain).

La procedura per il registrar GoDaddy, ad esempio, è la seguente:

1. Accedere al dominio personalizzato da usare e selezionarlo.

2. Nella sezione Domains (Domini) selezionare **Manage All** (Gestisci tutto), quindi selezionare **DNS** | **Manage Zones** (DNS | Gestisci zone).

3. In **Domain Name** (Nome di dominio) immettere il proprio nome di dominio, quindi selezionare **Search** (Cerca).

4. Nella pagina **DNS Management** (Gestione DNS) selezionare **Add** (Aggiungi), quindi selezionare **CNAME** nell'elenco **Type** (Tipo).

5. Completare i campi della voce CNAME:

    - Type (Tipo): lasciare selezionato *CNAME*.

    - Host: immettere il sottodominio del dominio personalizzato da usare. Ad esempio, www o il profilo.

    - Points to (Punta a): immettere il nome host predefinito della frontdoor. Ad esempio, contoso.azurefd.net. 

    - TTL: lasciare selezionato *1 Hour* (1 ora).

6. Selezionare **Salva**.
 
    La voce CNAME viene aggiunta alla tabella di record DNS.

7. Se si ha un record CNAME afdverify, selezionare l'icona a forma di matita accanto a esso, quindi selezionare l'icona del cestino.

8. Selezionare **Delete** (Elimina) per eliminare il record CNAME.


## <a name="clean-up-resources"></a>Pulire le risorse

Nei passaggi precedenti è stato aggiunto un dominio personalizzato a una frontdoor. Se non si vuole più associare la frontdoor a un dominio personalizzato, è possibile rimuovere il dominio personalizzato eseguendo questi passaggi:
 
1. Nella finestra di progettazione della frontdoor, selezionare il dominio personalizzato che si vuole rimuovere.

2. Fare clic su Elimina dal menu di scelta rapida per il dominio personalizzato.  

   Il dominio personalizzato viene dissociato dell'endpoint.


## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> - Creare un record DNS CNAME.
> - Associare il dominio personalizzato alla propria frontdoor.
> - Verificare il dominio personalizzato.