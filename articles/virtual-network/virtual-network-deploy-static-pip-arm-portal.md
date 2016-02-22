<properties 
   pageTitle="Distribuire una VM con un IP pubblico statico tramite il portale di Azure in Gestione risorse | Microsoft Azure"
   description="Scoprire come distribuire le VM con un IP pubblico statico tramite il portale di Azure in Gestione risorse"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/04/2016"
   ms.author="telmos" />

# Distribuire una VM con un IP pubblico statico tramite il portale di Azure

[AZURE.INCLUDE [virtual-network-deploy-static-pip-arm-selectors-include.md](../../includes/virtual-network-deploy-static-pip-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]Modello di distribuzione classica.

[AZURE.INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## Creare una VM con un IP pubblico statico 

Per creare una VM con un IP pubblico statico nel portale di Azure, seguire i passaggi riportati sotto.

1. Da un browser, passare al [portale di Azure](https://portal.azure.com) e, se necessario, accedere con l'account Azure.
2. Nell'angolo superiore sinistro del portale, fare clic su **Nuovo**>>**Calcolo**>**Windows Server 2012 R2 Datacenter**.
3. Nell'elenco **Selezionare un modello di distribuzione**, selezionare **Gestione risorse** e fare clic su **Crea**.
4. Nel pannello **Nozioni di base**, immettere le informazioni della VM come illustrato di seguito, quindi fare clic su **OK**.

	![Portale di Azure: Nozioni di base](./media/virtual-network-deploy-static-pip-arm-portal/figure1.png)

5. Nel pannello **Scegli una dimensione**, fare clic su **A1 Standard** come illustrato di seguito, quindi sul pulsante di **selezione**.

	![Portale di Azure: Scegliere una dimensione](./media/virtual-network-deploy-static-pip-arm-portal/figure2.png)

6. Nel pannello **Impostazioni**, fare clic su **Indirizzo IP pubblico**, quindi nel pannello **Crea indirizzo IP pubblico**, in **Assegnazione**, fare clic su **Statico** come illustrato di seguito. Fare quindi clic su **OK**.

	![Portale di Azure: Creare l'indirizzo IP pubblico](./media/virtual-network-deploy-static-pip-arm-portal/figure3.png)

7. Nel pannello **Impostazioni**, fare clic su **OK**.
8. Esaminare il pannello **Riepilogo**, come illustrato di seguito, quindi fare clic su **OK**.

	![Portale di Azure: Creare l'indirizzo IP pubblico](./media/virtual-network-deploy-static-pip-arm-portal/figure4.png)

9. Notare il nuovo riquadro nel dashboard.

	![Portale di Azure: Creare l'indirizzo IP pubblico](./media/virtual-network-deploy-static-pip-arm-portal/figure5.png)

10. Dopo aver creato la VM, il pannello **Impostazioni** viene visualizzato il pannello come illustrato di seguito

	![Portale di Azure: Creare l'indirizzo IP pubblico](./media/virtual-network-deploy-static-pip-arm-portal/figure6.png)

<!---HONumber=AcomDC_0211_2016-->