## Come creare una rete virtuale utilizzando un file di configurazione di rete nel portale di Azure

Azure utilizza un file xml per definire tutte le reti virtuali disponibili per una sottoscrizione. È possibile scaricare questo file e modificarlo per eliminare o modificare le reti virtuali esistenti oppure per crearne di nuove. In questo documento viene illustrato come scaricare questo file, definito come file di configurazione di rete (o netcgf), e modificarlo per creare una nuova rete virtuale. Per altre informazioni sul file di configurazione di rete, vedere [Schema di configurazione della rete virtuale di Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).

Per creare una rete virtuale utilizzando un file netcfg tramite il portale di Azure seguire questa procedura.

1. Da un browser, passare a http://manage.windowsazure.com e, se necessario, accedere con l'account Azure.
2. Scorrere verso il basso nell'elenco dei servizi e fare clic su **RETI** come indicato di seguito.

	![Reti virtuali di Azure](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure1.gif)

3. Nella parte inferiore della pagina fare clic sul pulsante **ESPORTA**, come illustrato di seguito.

	![Pulsante Esporta](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure2.png)

4. Nella pagina **Esporta configurazione di rete** selezionare la sottoscrizione da cui si desidera esportare la configurazione di rete virtuale e quindi fare clic sul pulsante del segno di spunta nell'angolo inferiore sinistro della pagina.
5. Seguire le istruzioni del browser per salvare il file **NetworkConfig.xml**. Assicurarsi di annotare il percorso in cui si sta salvando il file.
6. Aprire il file salvato nel passaggio 5 utilizzando un qualsiasi editor di testo o XML e cercare l’elemento **<VirtualNetworkSites>**. Se esistono altre reti già create, ogni rete sarà visualizzata con il relativo elemento **<VirtualNetworkSite>**.
7. Per creare la rete virtuale descritta in questo scenario, aggiungere il codice XML seguente sotto l’elemento **<VirtualNetworkSites>**:

		<VirtualNetworkSite name="TestVNet" Location="Central US">
		  <AddressSpace>
		    <AddressPrefix>192.168.0.0/16</AddressPrefix>
		  </AddressSpace>
		  <Subnets>
		    <Subnet name="FrontEnd">
		      <AddressPrefix>192.168.1.0/24</AddressPrefix>
		    </Subnet>
		    <Subnet name="BackEnd">
		      <AddressPrefix>192.168.2.0/24</AddressPrefix>
		    </Subnet>
		  </Subnets>
		</VirtualNetworkSite>

8.  Salvare il file di configurazione di rete.
9.  Nel portale di Azure, nell'angolo inferiore sinistro della pagina, fare clic su **NUOVO**, quindi fare clic su **SERVIZI DI RETE**, quindi su **RETE VIRTUALE** e su **IMPORTA CONFIGURAZIONE** come illustrato nella figura riportata di seguito.

	![Importa configurazione](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure3.gif)

10.  Nella pagina **Importa file di configurazione di rete** fare clic su **CERCA FILE...**, quindi passare alla cartella in cui il file è stato salvato nel passaggio 8 precedente, selezionare il file e quindi fare clic su **Apri**. La pagina web sarà simile alla figura riportata di seguito. Nell'angolo inferiore destro della pagina fare clic sul pulsante freccia per procedere al passaggio successivo.

	![Pagina Importa file di configurazione di rete](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure4.png)

11.   Nella pagina **Compilazione della rete** notare la voce relativa alla nuova rete virtuale, come illustrato nella figura riportata di seguito.

	![Pagina Compilazione della rete](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure5.png)

12.   Fare clic sul pulsante con il segno di spunta nell'angolo inferiore destro della pagina per creare la rete virtuale. Dopo alcuni secondi, la rete virtuale verrà visualizzata nell'elenco di reti virtuali disponibili, come illustrato nella figura seguente.

	![Nuova rete virtuale](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure6.png)

<!---HONumber=Oct15_HO3-->