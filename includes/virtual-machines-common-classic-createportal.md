

Una macchina virtuale *personalizzata* indica semplicemente una macchina virtuale creata usata una **app in primo piano** dal **Marketplace** poiché esegue gran parte del lavoro per l'utente. Tuttavia, è comunque possibile fare scelte di configurazione che includono i seguenti elementi:

* Collegare una macchina virtuale a Rete virtuale.
* Installare l'agente di macchina virtuale di Azure e le estensioni di macchina virtuale di Azure, ad esempio per antimalware.
* Aggiungere la macchina virtuale ai servizi cloud esistenti.
* Aggiungere la macchina virtuale a un account di archiviazione esistente.
* Aggiungere una macchina virtuale a un set di disponibilità.

<!--
> [!IMPORTANT]
> If you want your virtual machine to use a virtual network so you can connect to it directly by host name or set up cross-premises connections, make sure that you specify the virtual network when you create the virtual machine. A virtual machine can be configured to join a virtual network only when you create the virtual machine. For details on virtual networks, see [Azure Virtual Network overview](../articles/virtual-network/virtual-networks-overview.md).
>
>
 -->

> [!IMPORTANT]
> Se si desidera che una macchina virtuale usi una rete virtuale, assicurarsi di specificare la rete quando si crea la macchina virtuale.

> * Due vantaggi dell'uso di una rete virtuale consistono nel collegarsi direttamente alla macchina virtuale e nel configurare connessioni cross-premise.

> * È possibile configurare una macchina virtuale in modo da aggiungerla a una rete virtuale solo quando viene creata. Per informazioni dettagliate sulle reti virtuali, vedere [Panoramica di Rete virtuale](../articles/virtual-network/virtual-networks-overview.md).
>
>

## <a name="to-create-the-virtual-machine"></a>Per creare la macchina virtuale
