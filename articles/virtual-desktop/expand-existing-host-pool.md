---
title: Espandi pool host esistente con nuovi host sessione-Azure
description: Come espandere un pool di host esistente con nuovi host di sessione nel desktop virtuale di Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 07394fb2d33aaacdc8e47bc2d1809854c0c8ca1b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85209352"
---
# <a name="expand-an-existing-host-pool-with-new-session-hosts"></a>Espandi un pool di host esistente con nuovi host sessione

>[!IMPORTANT]
>Questo contenuto si applica all'aggiornamento di Primavera 2020 con gli oggetti Azure Resource Manager di Desktop virtuale Windows. Se si usa la versione Autunno 2019 di Desktop virtuale Windows senza gli oggetti Azure Resource Manager, vedere [questo articolo](./virtual-desktop-fall-2019/expand-existing-host-pool-2019.md).
>
> L'aggiornamento di Primavera 2020 di Desktop virtuale Windows è attualmente disponibile in anteprima pubblica. Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Quando si aumenta l'utilizzo all'interno del pool host, potrebbe essere necessario espandere il pool host esistente con nuovi host di sessione per gestire il nuovo carico.

In questo articolo viene illustrato come espandere un pool di host esistente con nuovi host di sessione.

## <a name="what-you-need-to-expand-the-host-pool"></a>Cosa è necessario per espandere il pool di host

Prima di iniziare, assicurarsi di aver creato un pool host e le macchine virtuali (VM) host sessione usando uno dei metodi seguenti:

- [Azure portal](./create-host-pools-azure-marketplace.md)
- [Creare un pool di host con PowerShell](./create-host-pools-powershell.md)

Al momento della creazione delle VM host della sessione e del pool host sono necessarie anche le informazioni seguenti:

- Dimensioni macchina virtuale, immagine e prefisso nome
- Credenziali di amministratore aggiunta a un dominio
- Nome della rete virtuale e della subnet

## <a name="add-virtual-machines-with-the-azure-portal"></a>Aggiungere macchine virtuali con la portale di Azure

Per espandere il pool di host aggiungendo macchine virtuali:

1. Accedere al portale di Azure.

2. Cercare e selezionare **Desktop virtuale Windows**.

3. Nel menu sul lato sinistro della schermata selezionare **pool host**, quindi selezionare il nome del pool di host a cui si desidera aggiungere macchine virtuali.

4. Selezionare **host sessione** dal menu sul lato sinistro della schermata.

5. Selezionare **+ Aggiungi** per avviare la creazione del pool host.

6. Ignorare la scheda nozioni di base e selezionare invece la scheda **Dettagli macchina virtuale** . Qui è possibile visualizzare e modificare i dettagli della macchina virtuale (VM) che si vuole aggiungere al pool host.

7. Selezionare il gruppo di risorse in cui si vogliono creare le macchine virtuali, quindi selezionare l'area. È possibile scegliere l'area corrente che si sta usando o una nuova area.

8. Immettere il numero di host di sessione che si desidera aggiungere al pool host in **numero di macchine virtuali**. Se, ad esempio, si espande il pool host di cinque host, immettere **5**.

    >[!NOTE]
    >Non è possibile modificare le dimensioni o l'immagine delle macchine virtuali perché è importante assicurarsi che tutte le macchine virtuali nel pool host abbiano le stesse dimensioni.

9. Per **informazioni sulle reti virtuali**, selezionare la rete virtuale e la subnet a cui si desidera creare un join delle macchine virtuali. È possibile selezionare la stessa rete virtuale attualmente usata dai computer esistenti oppure sceglierne una diversa più adatta all'area selezionata al passaggio 7.

10. Per l' **account amministratore**, immettere il nome utente e la password del dominio Active Directory associati alla rete virtuale selezionata. Queste credenziali verranno usate per aggiungere le macchine virtuali alla rete virtuale.

      >[!NOTE]
      >Assicurarsi che i nomi degli amministratori siano conformi alle informazioni fornite qui. E che nell'account non sia abilitata l'autenticazione a più fattori.

11. Selezionare la scheda **tag** se sono presenti tag con i quali si desidera raggruppare le macchine virtuali. In caso contrario, ignorare questa scheda.

12. Selezionare la scheda **Verifica + crea** . rivedere le scelte effettuate e, se il tutto sembra corretto, selezionare **Crea**.

## <a name="next-steps"></a>Passaggi successivi

Ora che il pool host esistente è stato espanso, è possibile accedere a un client desktop virtuale di Windows per testarli come parte di una sessione utente. È possibile connettersi a una sessione con uno dei client seguenti:

- [Connettersi con il client desktop di Windows](./connect-windows-7-and-10.md)
- [Connettersi con il client Web](./connect-web.md)
- [Connettersi con il client Android](./connect-android.md)
- [Connettersi con il client macOS](./connect-macos.md)
- [Connettersi con il client iOS](./connect-ios.md)
