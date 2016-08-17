## Scenario

Per illustrare meglio come creare una rete virtuale e le relative subnet, in questo documento verrà utilizzato lo scenario seguente.

![Scenario di una rete virtuale](./media/virtual-networks-create-vnet-scenario-include/vnet-scenario.png)

In questo scenario verrà creata una rete virtuale denominata **TestVNet** con un blocco CIDR riservato di **192.168.0.0./16**. Una rete virtuale conterrà le subnet seguenti:

- **FrontEnd**, che utilizza **192.168.1.0/24** come relativo blocco CIDR.
- **BackEnd**, che utilizza **192.168.2.0/24** come relativo blocco CIDR.

 

<!---HONumber=AcomDC_0803_2016-->