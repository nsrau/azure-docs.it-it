<properties
   pageTitle="Aggiornare i certificati per un cluster Azure | Microsoft Azure"
   description="Descrive come caricare un nuovo certificato nell’insieme di credenziali delle chiavi di Azure e aggiornare o rimuovere un certificato per un cluster Service Fabric esistente."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/27/2016"
   ms.author="chackdan"/>

# Aggiungere o rimuovere certificati per un cluster Service Fabric in Azure

Service Fabric consente di specificare due certificati (uno primario e uno secondario) durante la configurazione della sicurezza del certificato al momento della creazione del cluster. Per impostazione predefinita, quello specificato in fase di creazione è il certificato primario. Dopo la creazione del cluster, è possibile aggiungere un nuovo certificato come secondario oppure rimuoverne uno esistente. Per altre informazioni su come Service Fabric utilizza i certificati X.509, leggere [Scenari di sicurezza del cluster](service-fabric-cluster-security.md).

>[AZURE.NOTE] Per un cluster sicuro, sarà sempre necessario almeno un certificato (primario o secondario) valido (non revocato né scaduto) distribuito. In caso contrario, non sarà possibile accedere al cluster.

## Aggiungere un certificato secondario
Per aggiungere un altro certificato come secondario, è necessario caricarlo in un insieme di credenziali delle chiavi di Azure e quindi distribuirlo per le macchine virtuali nel cluster. Per altre informazioni, vedere il blog relativo alla [distribuzione di certificati nelle macchine virtuali dall'insieme di credenziali delle chiavi gestito dal cliente](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx).

1. [Caricare un certificato X.509 nell’insieme di credenziali delle chiavi](service-fabric-secure-azure-cluster-with-certs.md#step-2-upload-the-x509-certificate-to-the-key-vault)
2. Accedere al [portale di Azure](https://portal.azure.com/) e passare alla risorsa del cluster a cui si vuole aggiungere questo certificato.
3. In **Impostazioni** fare clic sull'impostazione relativa ai certificati e immettere l'identificazione personale del certificato secondario.
4. Fare clic su **Save**. Verrà avviata una distribuzione. Se questa viene completata correttamente, sarà possibile usare il certificato primario o quello secondario per eseguire operazioni di gestione sul cluster.

![Schermata delle identificazioni personali del certificato nel portale] [SecurityConfigurations\_02]

## Rimuovere un certificato
Ecco la procedura per rimuovere un certificato precedente in modo che il cluster non lo usi:

1. Accedere al [portale di Azure](https://portal.azure.com/) e passare alle impostazioni di sicurezza del cluster.
2. Rimuovere uno dei certificati.
3. Fare clic su **Salva**. Verrà avviata una nuova distribuzione. Una volta completata la nuova distribuzione, il certificato rimosso non potrà più essere usato per connettersi al cluster.

## Passaggi successivi
Per ulteriori informazioni sulla gestione del cluster, leggere questi articoli:

- [Processo di aggiornamento del cluster di infrastruttura di servizi e operazioni eseguibile dall'utente](service-fabric-cluster-upgrade.md)
- [Configurare l’accesso in base al ruolo per i client](service-fabric-cluster-security-roles.md)


<!--Image references-->
[SecurityConfigurations_02]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_02.png

<!---HONumber=AcomDC_0720_2016-->