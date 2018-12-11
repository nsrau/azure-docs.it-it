---
title: 'Avvio rapido: Creare una zona e un record DNS con il portale di Azure'
description: Usare questa guida di avvio rapido dettagliata per informazioni su come creare una zona e un record DNS di Azure usando il portale di Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: quickstart
ms.date: 12/4/2018
ms.author: victorh
ms.openlocfilehash: f54a9e40c4f75704e66c4a3a90ad4b293d1e6309
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2018
ms.locfileid: "52889226"
---
# <a name="quickstart-configure-azure-dns-for-name-resolution-by-using-the-portal"></a>Avvio rapido: Configurare DNS di Azure per la risoluzione dei nomi con il portale di Azure

È possibile configurare DNS di Azure per la risoluzione dei nomi host nel dominio pubblico. Ad esempio, se è stato acquistato il nome di dominio *contoso.com* da un registrar di nomi di dominio, è possibile configurare DNS di Azure in modo da ospitare il dominio *contoso.com* e risolvere *www.contoso.com* nell'indirizzo IP del server Web o dell'app Web.

In questo avvio rapido verrà creato un dominio di test e quindi un record di indirizzo per risolvere *www* nell'indirizzo IP *10.10.10.10*.

>[!IMPORTANT]
>Tutti i nomi e gli indirizzi IP in questo avvio rapido sono esempi che non rappresentano scenari reali. Ove applicabile, l'avvio rapido illustra anche le implicazioni nel mondo reale.

<!---
You can also perform these steps using [Azure PowerShell](dns-getstarted-powershell.md) or the cross-platform [Azure CLI](dns-getstarted-cli.md).
--->

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

Per tutti i passaggi del portale, accedere al [portale di Azure](https://portal.azure.com).

## <a name="create-a-dns-zone"></a>Creare una zona DNS

Una zona DNS contiene le voci DNS per un dominio. Per iniziare a ospitare il dominio in DNS di Azure, creare una zona DNS per il nome di dominio. 

**Per creare la zona DNS:**

1. In alto a sinistra selezionare **Crea una risorsa**, quindi **Rete**e quindi **Zona DNS**.
   
1. Nella pagina **Crea zona DNS** digitare o selezionare i valori seguenti:
   
   - **Nome**: digitare *contoso.xyz* per questo esempio di avvio rapido. Il nome della zona DNS può essere qualsiasi valore che non sia già configurato nei server DNS di Azure. Un valore reale sarebbe un dominio acquistato da un registrar di nomi di dominio.
   - **Gruppo di risorse**: selezionare **Crea nuovo**, immettere *dns-test*e selezionare **OK**. Il nome del gruppo di risorse deve essere univoco all'interno della sottoscrizione di Azure. 
   
1. Selezionare **Create**.

   ![Zona DNS](./media/dns-getstarted-portal/openzone650.png)
   
La creazione della zona può richiedere alcuni minuti.

## <a name="create-a-dns-record"></a>Creare un record DNS

Creare voci o record DNS per il dominio all'interno della zona DNS. Creare un nuovo record di indirizzo o record "A" per risolvere un nome host in un indirizzo IPv4.

**Per creare un record "A":**

1. Nel portale di Azure in **Tutte le risorse** aprire la zona DNS **contoso.xyz** nel gruppo di risorse **dns-test**. È possibile immettere *contoso.xyz* nella finestra **Filtra per nome** per trovarla più facilmente.

1. Nella parte superiore della pagina **Zona DNS** selezionare **+ Set di record**.

1. Nella pagina **Aggiungi set di record** digitare o selezionare i valori seguenti:

   - **Nome**: digitare *www*. Il nome del record è il nome host che si vuole risolvere nell'indirizzo IP specificato.
   - **Tipo**: selezionare **A**. I record "A" sono i più comuni, ma esistono altri tipi di record per server di posta elettronica ("MX"), indirizzi IPv6 ("AAAA") e così via. 
   - **TTL**: digitare *1*. La *durata TTL*  della richiesta DNS specifica per quanto tempo i client e i server DNS possono memorizzare nella cache una risposta.
   - **Unità TTL**: selezionare **Ore**. Questo è l'unità di tempo per il valore **TTL**. 
   - **Indirizzo IP**: per questo esempio di avvio rapido, digitare *10.10.10.10*. Questo valore indica l'indirizzo IP in cui viene risolto il record "A". In uno scenario reale, sarebbe necessario immettere l'indirizzo IP pubblico per il server Web.

Poiché questo avvio rapido non usa un dominio reale, non è necessario configurare i server dei nomi DNS di Azure presso un registrar. Con un dominio reale, si vorrebbe consentire a qualsiasi utente su Internet di risolvere il nome host per connettersi al server o all'app Web. Si visiterebbe quindi il registrar per sostituire i record del server dei nomi con i server dei nomi DNS di Azure. Per altre informazioni, vedere [Esercitazione: Ospitare un dominio in DNS di Azure](dns-delegate-domain-azure-dns.md#delegate-the-domain).

## <a name="test-the-name-resolution"></a>Testare la risoluzione dei nomi

Dopo aver creato una zona DNS di test, con un record "A" di test, è possibile testare la risoluzione dei nomi con uno strumento denominato *nslookup*. 

**Per testare la risoluzione dei nomi DNS:**

1. Nel portale di Azure in **Tutte le risorse** aprire la zona DNS **contoso.xyz** nel gruppo di risorse **dns-test**. È possibile immettere *contoso.xyz* nella finestra **Filtra per nome** per trovarla più facilmente.

1. Copiare uno dei nomi dall'elenco di server dei nomi nella pagina **Panoramica**. 
   
   ![zona](./media/dns-getstarted-portal/viewzonens500.png)
   
   >[!NOTE]
   >In uno scenario reale, si copiano tutti e quattro i nomi dei server dei nomi, inclusi i punti finali, e si usano per i nuovi nomi di server dei nomi DNS di Azure presso il registrar. Per altre informazioni, vedere [Delegare un dominio al servizio DNS di Azure](dns-delegate-domain-azure-dns.md).
   
1. Aprire un prompt dei comandi ed eseguire il comando seguente:

   ```
   nslookup <host name> <name server name>
   ```
   
   Ad esempio: 
   
   ```
   nslookup www.contoso.xyz ns1-08.azure-dns.com.
   ```
   
   Verrà visualizzata una schermata simile alla seguente:
   
   ![nslookup](media/dns-getstarted-portal/nslookup.PNG)

Il nome host **www.contoso.xyz** viene risolto in **10.10.10.10**, esattamente come è stato configurato. Questo risultato conferma il corretto funzionamento della risoluzione dei nomi. 

## <a name="clean-up-resources"></a>Pulire le risorse

Quando le risorse create in questo avvio rapido non sono più necessarie, rimuoverle eliminando il gruppo di risorse **dns-test**. Aprire il gruppo di risorse **dns-test** e quindi scegliere **Elimina gruppo di risorse**.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare record DNS per un'app Web in un dominio personalizzato](./dns-web-sites-custom-domain.md)