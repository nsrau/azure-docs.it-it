Peering reti virtuale è un meccanismo che consente di connettere due reti virtuali nella stessa area tramite la rete backbone di Azure. Una volta eseguito il peering, le due reti virtuali appariranno come una singola rete virtuale per qualsiasi scopo di connettività. Se non si ha familiarità con il peering reti virtuali, vedere la panoramica del [Peering reti virtuali](../articles/virtual-network/virtual-network-peering-overview.md).

Il peering reti virtuali è in anteprima pubblica. Per usarlo, è necessario registrarsi con il comando seguente:

    Register-AzureRmProviderFeature -FeatureName AllowVnetPeering -ProviderNamespace Microsoft.Network

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
 

<!---HONumber=AcomDC_0810_2016-->