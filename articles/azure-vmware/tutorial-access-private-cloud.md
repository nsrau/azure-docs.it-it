---
title: 'Esercitazione: Informazioni su come accedere al cloud privato'
description: Informazioni su come accedere a un cloud privato della soluzione Azure VMware
ms.topic: tutorial
ms.date: 08/21/2020
ms.openlocfilehash: 73226c6aa567dc5fbe18251bed4812637664a02c
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750527"
---
# <a name="tutorial-learn-how-to-access-an-azure-vmware-solution-private-cloud"></a>Esercitazione: Informazioni su come accedere a un cloud privato della soluzione Azure VMware

Durante la fase di anteprima la soluzione Azure VMware non consente di gestire il cloud privato con un'istanza locale di vCenter. Sarà necessario eseguire ulteriori operazioni di configurazione e connessione a un'istanza locale di vCenter tramite un jumpbox. 

In questa esercitazione si creerà una macchina virtuale Windows per un jumpbox nel gruppo di risorse creato nell'esercitazione precedente, [Esercitazione: Configurare la rete per il cloud privato VMware in Azure](tutorial-configure-networking.md), e si eseguirà l'accesso a vCenter. Si tratta di una VM collegata alla stessa rete virtuale creata che fornisce l'accesso a vCenter e a NSX Manager. 

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare una macchina virtuale Windows da usare per connettersi a vCenter
> * Accedere a vCenter dalla macchina virtuale

## <a name="create-a-new-windows-virtual-machine"></a>Creare una nuova macchina virtuale Windows

1. Nel gruppo di risorse selezionare **+ Aggiungi**, quindi cercare e selezionare **Microsoft Windows 10** e fare clic su **Crea**.

   :::image type="content" source="./media/tutorial-access-private-cloud/ss8-azure-w10vm-create.png" alt-text="Aggiungere una nuova macchina virtuale Windows 10 per un jumpbox." border="true":::

1. Immettere le informazioni obbligatorie nei campi e quindi selezionare **Verifica e crea**. Per altre informazioni sui campi, vedere la tabella seguente.

   | Campo | valore |
   | --- | --- |
   | **Sottoscrizione** | Questo valore è già popolato con la sottoscrizione a cui appartiene il gruppo di risorse. |
   | **Gruppo di risorse** | Questo valore è già popolato con il gruppo di risorse corrente. Dovrebbe essere il gruppo di risorse creato nell'esercitazione precedente. |
   | **Nome macchina virtuale** | Immettere un nome univoco per la VM. |
   | **Area** | Selezionare la posizione geografica della VM. |
   | **Opzioni di disponibilità** | Lasciare selezionato il valore predefinito. |
   | **Immagine** | Selezionare l'immagine della VM. |
   | **Dimensione** | Lasciare il valore predefinito. |
   | **Tipo di autenticazione**  | selezionare **Password**. |
   | **Nome utente** | Immettere il nome utente per l'accesso alla VM. |
   | **Password** | Immettere la password per l'accesso alla VM. |
   | **Conferma password** | Immettere la password per l'accesso alla VM. |
   | **Porte in ingresso pubbliche** | Selezionare **Nessuno**. Se si seleziona Nessuno, è possibile usare l'[accesso JIT](../security-center/security-center-just-in-time.md#jit-configure) per controllare l'accesso alla macchina virtuale solo quando si vuole accedervi.  |

1. Dopo aver immesso le informazioni appropriate, fare clic su **Rivedi e crea**. 
1. Al termine della convalida, selezionare **Crea** per avviare il processo di creazione della macchina virtuale.

   :::image type="content" source="./media/tutorial-access-private-cloud/ss11-review-create-wjb01.png" alt-text="Creare una nuova macchina virtuale Windows 10 per un jumpbox." border="true":::

## <a name="connect-to-the-local-vcenter-of-your-private-cloud"></a>Connettersi all'istanza locale di vCenter del cloud privato

1. Dal jumpbox accedere al client vSphere client con SSO di VMware vCenter. Accedere al client vSphere usando un nome utente di amministratore del cloud, accettare il rischio per la sicurezza e scegliere di continuare quando viene visualizzato un avviso, accedere a VMware vCenter con le credenziali Single Sign-On e verificare che l'interfaccia utente venga visualizzata correttamente.

1. Nel portale di Azure selezionare il cloud privato e quindi nella visualizzazione **Panoramica** selezionare **Identità > Impostazione predefinita**. Vengono visualizzati gli URL e le credenziali di accesso per vCenter e NSX-T Manager del cloud privato.

   :::image type="content" source="./media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="Visualizzazione di URL e credenziali di vCenter e NSX Manager del cloud privato." border="true":::

1. Passare alla macchina virtuale creata nel passaggio precedente e connettersi. Per informazioni dettagliate su come connettersi alla macchina virtuale, vedere [Connettersi a una macchina virtuale](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine)

1. Nella VM Windows aprire un browser e passare agli URL di vCenter e NSX-T Manager in due schede. Nella scheda vCenter immettere le credenziali utente `cloudadmin@vmcp.local` del passaggio precedente.

   :::image type="content" source="./media/tutorial-access-private-cloud/ss5-vcenter-login.png" alt-text="Accedere a vCenter del cloud privato." border="true":::

   :::image type="content" source="./media/tutorial-access-private-cloud/ss6-vsphere-client-home.png" alt-text="Portale di vCenter." border="true":::

1. Nella seconda scheda del browser accedere a NSX-T Manager.

   :::image type="content" source="./media/tutorial-access-private-cloud/ss10-nsx-manager-home.png" alt-text="Nella seconda scheda del browser accedere a NSX-T Manager." border="true":::

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]
> * Creare una macchina virtuale Windows da usare per connettersi a vCenter
> * Accedere a vCenter dalla macchina virtuale

Continuare con l'esercitazione successiva per informazioni su come ridimensionare il cloud privato della soluzione Azure VMware.

> [!div class="nextstepaction"]
> [Dimensionare un cloud privato della soluzione Azure VMware](tutorial-scale-private-cloud.md)
