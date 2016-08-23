<properties
   pageTitle="Configurare un gateway applicazione per l'offload SSL con il portale | Microsoft Azure"
   description="Questa pagina contiene istruzioni per creare un gateway applicazione con offload SSL usando il portale"
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>  
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/09/2016"
   ms.author="gwallace"/>  

# Configurare un gateway applicazione per l'offload SSL con il portale

> [AZURE.SELECTOR]
-[Azure portal](application-gateway-ssl-portal.md)
-[Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)
-[Azure Classic PowerShell](application-gateway-ssl.md)

Il gateway applicazione di Azure può essere configurato per terminare la sessione Secure Sockets Layer (SSL) nel gateway ed evitare costose attività di decrittografia SSL nella Web farm. L'offload SSL semplifica anche la configurazione e la gestione del server front-end dell'applicazione Web.

## Scenario

Lo scenario seguente illustra la configurazione dell'offload SSL in un gateway applicazione esistente. Lo scenario presuppone che sia già stata seguita la procedura per [creare un gateway applicazione](application-gateway-create-gateway-portal.md).

## Prima di iniziare

Per configurare l'offload SSL con un gateway applicazione è necessario un certificato, che viene caricato nel gateway applicazione e viene usato per crittografare e decrittografare il traffico inviato tramite SSL. Il certificato deve essere in formato PFX (Personal Information Exchange). Questo formato di file consente l'esportazione della chiave privata necessaria al gateway applicazione per eseguire la crittografia e la decrittografia del traffico.

## Aggiungere un listener HTTPS

Il listener HTTPS cerca il traffico in base alla relativa configurazione e consente di instradare il traffico ai pool back-end.

### Passaggio 1

Passare al portale di Azure e selezionare un gateway applicazione esistente.

![Pannello di panoramica del gateway applicazione][1]

### Passaggio 2

Fare clic su Listener e quindi sul pulsante Aggiungi per aggiungere un nuovo listener.

### Passaggio 3

Inserire le informazioni necessarie per il listener e caricare il certificato PFX. Al termine, fare clic su OK.

**Nome**: nome descrittivo del listener. **Configurazione IP front-end**: configurazione IP front-end usata per il listener. **Porta front-end (Nome/Porta)**: nome descrittivo della porta usata nel front-end del gateway applicazione e porta effettiva usata. **Protocollo**: opzione che consente di determinare se usare HTTPS o HTTP per il front-end. **Certificato (Nome/Password)**: se si usa l'offload SSL, per questa impostazione sono necessari un certificato PFX, un nome descrittivo e una password.

![Pannello Aggiungi listener][2]  

## Creare una regola e associarla al listener

Ora che il listener è stato creato, è necessario creare una regola per gestire il traffico dal listener.

### Passaggio 1

Fare clic su **Regole** nel gateway applicazione e quindi su Aggiungi.

![Pannello delle regole del gateway applicazione][3]  

### Passaggio 2

Nel pannello **Aggiungi regola di base** digitare il nome descrittivo della regola e scegliere il listener creato nel passaggio precedente. Scegliere il pool back-end e l'impostazione HTTP appropriati e fare clic su **OK**.

![Finestra delle impostazioni HTTP][4]  

Le impostazioni vengono così salvate nel gateway applicazione. Il processo di salvataggio di queste impostazioni può richiedere tempo e le impostazioni potrebbero non essere immediatamente visualizzabili tramite il portale o PowerShell. Al termine del salvataggio, il gateway applicazione gestisce la crittografia e la decrittografia del traffico. Tutto il traffico tra il gateway applicazione e i server Web back-end verrà gestito su HTTP. Tutte le comunicazioni verso il client, se avviate su HTTPS, verranno restituite al client crittografate.

## Passaggi successivi

Per informazioni su come configurare un probe di integrità personalizzato con un gateway applicazione di Azure, vedere [Creare un gateway applicazione con il portale](application-gateway-create-gateway-portal.md).

[1]: ./media/application-gateway-ssl-portal/figure1.png
[2]: ./media/application-gateway-ssl-portal/figure2.png
[3]: ./media/application-gateway-ssl-portal/figure3.png
[4]: ./media/application-gateway-ssl-portal/figure4.png

<!---HONumber=AcomDC_0810_2016-->