### DNS 
DNS stands for (Domain name system) is like a phonebook of the internet ,when user want to access a page he writes a **Domain name** for this page like www.Facebook.com, but browsers can only interact through **Internet Protocal (IP)** address like 192.168.1.1 so DNS translate Domain name to IP so browser can access the page . 

There is 4 different types of DNS that work togather to find the acutally URL that you want 
1. **DNS Resolver(Recursor)**
    You can thought DNS Rsolver as a libararian who is asked to go find a particular book somewhere in a library.It is 
    designed to recieve quries from client and then it is responsible for making additional requests in order to satisfy the client’s DNS query.
1. **Root nameserver** 
    The root server is the first step in translating (resolving) human readable host names into IP addresses. It can be thought of like an index in a library that points to different racks of books - typically it serves as a reference to other more specific locations.
1. **TLD nameserver** 
   The top level domain server (TLD) can be thought of as a specific rack of books in a library. This nameserver is the next step in the search for a specific IP address, and it hosts the last portion of a hostname (In example.com, the TLD server is “com”).
1. **Authoritative nameserver**
   This final nameserver can be thought of as a dictionary on a rack of books, in which a specific name can be translated into its definition. The authoritative nameserver is the last stop in the nameserver query. If the authoritative name server has access to the requested record, it will return the IP address for the requested hostname back to the DNS Recursor (the librarian) that made the initial request.
![pic](https://cf-assets.www.cloudflare.com/slt3lc6tev37/1NzaAqpEFGjqTZPAS02oNv/bf7b3f305d9c35bde5c5b93a519ba6d5/what_is_a_dns_server_dns_lookup.png)