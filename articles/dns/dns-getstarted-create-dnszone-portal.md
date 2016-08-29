<properties
   pageTitle="Come creare e gestire una zona DNS nel portale di Azure | Microsoft Azure"
   description="Informazioni su come creare le zone DNS per DNS di Azure. Si tratta di una guida dettagliata per creare e gestire il primo DNS e iniziare a ospitare il dominio DNS con il portale di Azure."
   services="dns"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/16/2016"
   ms.author="cherylmc"/>  

# Creare una zona DNS nel portale di Azure


> [AZURE.SELECTOR]
- [Portale di Azure](dns-getstarted-create-dnszone-portal.md)
- [PowerShell](dns-getstarted-create-dnszone.md)
- [Interfaccia della riga di comando di Azure](dns-getstarted-create-dnszone-cli.md)



Questo articolo illustra i passaggi per creare una zona DNS con il portale di Azure. È anche possibile creare una zona DNS con PowerShell o l'interfaccia della riga di comando.

[AZURE.INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]


### Informazioni sui tag per DNS di Azure


I tag sono un elenco di coppie nome-valore usate da Azure Resource Manager per etichettare le risorse a scopo di fatturazione o di raggruppamento. Per altre informazioni sui tag, vedere l'articolo [Uso dei tag per organizzare le risorse di Azure](../resource-group-using-tags.md).

È possibile aggiungere tag nel portale di Azure con il pannello **Impostazioni** per la zona DNS.


## Creare una zona DNS

1. Accedere al portale di Azure

2. Nel menu Hub fare clic su **New > Rete >**, quindi scegliere **Zona DNS** per aprire il pannello della zona DNS.
 
	![Zona DNS](./media/dns-getstarted-create-dnszone-portal/openzone650.png)

3. Nella parte inferiore del pannello **Zona DNS** fare clic su **Crea**. Viene aperto il pannello **Crea zona DNS**.

	![Crea zona](./media/dns-getstarted-create-dnszone-portal/newzone250.png)

4. Nel pannello **Crea zona DNS** assegnare un nome alla zona DNS. ad esempio *contoso.com*. Vedere [Informazioni sui nomi delle zone DNS](#names) nella sezione precedente.

5. Successivamente, specificare il gruppo di risorse da usare. È possibile creare un nuovo gruppo di risorse o selezionarne uno già esistente.

6. Specificare il percorso del gruppo di risorse nell'elenco a discesa **Percorso**. Si noti che questa impostazione si riferisce al percorso del gruppo di risorse, non al percorso della zona DNS. La risorsa di zona DNS effettiva è automaticamente "globale" e non può, o deve, essere specificata nel portale.

7. È possibile lasciare selezionata la casella di controllo **Aggiungi al dashboard** per trovare facilmente la nuova zona nel dashboard. Fare quindi clic su **Crea**.

	![Aggiungi al dashboard](./media/dns-getstarted-create-dnszone-portal/pindashboard150.png)

8. Dopo aver fatto clic su Crea, la nuova zona viene configurata nel dashboard.

	![Creating](./media/dns-getstarted-create-dnszone-portal/creating150.png)

9. Dopo aver creato la nuova zona, nel dashboard viene aperto il pannello per la nuova zona.


## Visualizzare i record

La creazione di una zona DNS comporta anche la creazione dei record seguenti:

- Il record Origine di autorità (SOA), presente nella radice di ogni zona DNS.
- I record del server del nomi autorevole (NS), che mostrano quali server dei nomi ospitano la zona. DNS di Azure usa un pool di server dei nomi e dunque diversi server dei nomi potrebbero essere assegnati ad aree diverse nel servizio DNS di Azure. Per altre informazioni, vedere [Delegare un dominio a DNS di Azure](dns-domain-delegation.md).

È possibile visualizzare i record dal portale di Azure

1. Dal pannello **Zona DNS** fare clic su **Tutte le impostazioni** per aprire il **pannello Impostazioni** per la zona DNS.

	![zona](./media/dns-getstarted-create-dnszone-portal/viewzonens500.png)


2. Nella parte inferiore del riquadro Informazioni di base è possibile visualizzare i set di record per la zona DNS.


	![zona](./media/dns-getstarted-create-dnszone-portal/viewzone500.png)

## Test

È possibile testare la zona DNS usando strumenti DNS come nslookup, DIG o il [cmdlet di PowerShell Resolve-DnsName](https://technet.microsoft.com/library/jj590781.aspx).

Se non è stato ancora delegato il dominio per usare la nuova zona in DNS di Azure, sarà necessario indirizzare la query DNS direttamente a uno dei server dei nomi per la zona. I server dei nomi per la zona sono specificati nei record NS, ottenuti tramite il comando `Get-AzureRmDnsRecordSet` precedente. Assicurarsi di sostituire i valori corretti per la propria zona nel comando seguente.

	nslookup
	> set type=SOA
	> server ns1-01.azure-dns.com
	> contoso.com

	Server: ns1-01.azure-dns.com
	Address:  208.76.47.1

	contoso.com
        	primary name server = ns1-01.azure-dns.com
        	responsible mail addr = msnhst.microsoft.com
        	serial  = 1
        	refresh = 900 (15 mins)
        	retry   = 300 (5 mins)
        	expire  = 604800 (7 days)
        	default TTL = 300 (5 mins)



## Eliminare una zona DNS

È possibile eliminare la zona DNS direttamente dal portale. Prima di eliminare una zona DNS di DNS di Azure, sarà necessario eliminare tutti i set di record, ad eccezione dei record NS e SOA alla radice della zona in cui sono stati creati automaticamente quando è stata creata la zona.

1. Individuare il pannello **Zona DNS** per la zona da eliminare, quindi fare clic su **Elimina** nella parte superiore del pannello.
 
2. Viene visualizzato un messaggio che informa che è necessario eliminare tutti i set di record, tranne i record NS e SOA creati automaticamente. Se i set di record sono stati eliminati, fare clic su **Sì**. Si noti che quando si elimina una zona DNS dal portale, non viene eliminato il gruppo di risorse associato alla zona DNS.


## Passaggi successivi

Dopo aver creato una zona DNS, creare [set di record e record](dns-getstarted-create-recordset-portal.md) per avviare la risoluzione dei nomi per il dominio Internet.

<!---HONumber=AcomDC_0817_2016-->