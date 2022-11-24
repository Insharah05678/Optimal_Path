#  Node Class 
import matplotlib.pyplot as plt
from PIL import Image

Image1 = Image.open("projrct_pic.png")
Image1.show()

class Node:
        
    def __init__(self, name, parent, g, h, f):                                          # Initializing the class
        self.name = name
        self.parent = parent
        self.g = g                                                                      # Distance to start node
        self.h = h                                                                      # Distance to goal node
        self.f = f                                                                      # Total cost
            
    def __eq__(self, other):                                                            # Comparing two nodes
        return self.name == other.name
    
    def __lt__(self, other):                                                            # Sorting nodes
        return self.f < other.f
    
    def __repr__(self):                                                                 # Printing nodes
        return ('({0},{1})'.format(self.name, self.f))
    
    def printNode(self):                                                                # Customized Printing of nodes
        print(self.name, end = " - ")
        print(self.parent, end = " : ")
        print(self.g, end = " : ")
        print(self.h, end=" : ")
        print(self.f)


#  Graph Class 

class Graph:
    
    def __init__(self, graph_dict=None, directed=True):                                 # Initialize the class
        self.graph_dict = graph_dict or {}
        self.directed = directed
        if not directed:
            self.make_undirected()
                
    def make_undirected(self):                                                          # Create an undirected graph by adding symmetric edges
        for a in list(self.graph_dict.keys()):
            for (b, dist) in self.graph_dict[a].items():
                self.graph_dict.setdefault(b, {})[a] = dist
                    
    def connect(self, A, B, distance=1):                                                # Add a link from A and B of given distance, and also add the inverse link if the graph is undirected
        self.graph_dict.setdefault(A, {})[B] = distance
        if not self.directed:
            self.graph_dict.setdefault(B, {})[A] = distance
               
    def get(self, a, b=None):                                                           # Get neighbors or a neighbor
        links = self.graph_dict.setdefault(a, {})
        if b is None:
            return links
        else:
            return links.get(b)
            
    def nodes(self):                                                                    # Return a list of nodes in the graph
        s1 = set([k for k in self.graph_dict.keys()])
        s2 = set([k2 for v in self.graph_dict.values() for k2, v2 in v.items()])
        nodes = s1.union(s2)
        return list(nodes)

    def getNode(self, city, heuristics, end):                                           # Get a specific neighbour which has minimum cost
        nodes = list()
        min = 999
        for (b,dist) in self.graph_dict[city].items():
            if(b == end):
                return Node(city, b, dist, heuristics[b], dist+heuristics[b] )
            nodes.append(Node(city, b, dist, heuristics[b], dist+heuristics[b] ))
            if (dist+heuristics[b]) < min:
                min = dist+heuristics[b]
                minnode = Node(city, b, dist, heuristics[b], dist+heuristics[b] )       
        return minnode
        
    def printgraph(self):                                                               # Function to print each edge in the entire graph
         for a in list(self.graph_dict.keys()):
            for (b, dist) in self.graph_dict[a].items():
                print (self.graph_dict.setdefault(a,{})[b], end = " : ")
                print(a, end = " - ")
                print(b)


#  A* function
        
def A_Star(graph, heuristics, start, end):
    open_list = list()
    closed_list = list()  
    path = list()                                                                       # Will store the path we are taking
    curr_node = graph.getNode(start,heuristics, end)                                    # Starting node
    open_list.append(curr_node)
    totalcost = 0

    if(end not in graph.graph_dict):                                                    # Incase the goal state does not exist
        print("\n\n---------------------------\nGOAL STATE DOES NOT EXIST\n---------------------------\n\n")
        return  None

    while(curr_node.name != end):                                                       # Runs Until we cannot find the goal state or
        totalcost += curr_node.g
        path.append(curr_node.name)
        curr_node = open_list.pop()
        closed_list.append(curr_node)
        curr_node = graph.getNode(curr_node.parent,heuristics, end)
        open_list.append(curr_node)
        if(curr_node.name == end):
            path.append(curr_node.name)
            break

    print("\nFINAL COST -> " + str(totalcost))
    return path

# Main function


# The main entry point for this module
def main():
    graph = Graph()
        
    # distance in Kilometer
    graph.connect('North Karachi', 'Sakhi Hassan', 3)
    graph.connect('North Karachi', 'Nagan Chowrangi', 2)
    graph.connect('North Karachi', 'Gulberg', 90)
    graph.connect('Sakhi Hassan', 'Five Star Chowrangi', 5)
    graph.connect('Five Star Chowrangi', 'Nagan Chowrangi', 17)
    graph.connect('Nagan Chowrangi', 'Star Gate', 150)
    graph.connect('Nagan Chowrangi', 'Malir Cantt', 192)
    graph.connect('Malir Cantt', 'Malir Halt', 14)
    graph.connect('Gulberg', 'Rashid Minhas Road', 45)
    graph.connect('Rashid Minhas Road','Shahrah-e-Faisal', 13)
    graph.connect('Shahrah-e-Faisal', 'Gulshan Iqbal',7)
    graph.connect('Gulshan Iqbal', 'Airport Road', 4)
    graph.connect('Airport Road','Malir Cantt', 19)
    graph.connect('Airport Road','Malir Halt', 3)
    graph.connect('Star Gate', 'Jinnah Airport', 6)
    graph.connect('Malir Halt', 'Jinnah Airport', 3)
    graph.connect('Gulberg', 'Jinnah Airport', 104)
        
        
    #  graph undirected
    graph.make_undirected()
        
    # Create heuristics value
    heuristics = {}
    heuristics['North Karachi'] = 366
    heuristics['Jinnah Airport'] = 40
    heuristics['Airport Road'] = 160
    heuristics['Gulshan Iqbal'] = 242
    heuristics['Star Gate'] = 176
    heuristics['Rashid Minhas Road'] = 244
    heuristics['Shahrah-e-Faisal'] = 241
    heuristics['Five Star Chowrangi'] = 380
    heuristics['Malir Halt'] = 193
    heuristics['Malir Cantt'] = 374 
    heuristics['Nagan Chowrangi'] = 70
    heuristics['Gulberg'] = 329
    heuristics['Sakhi Hassan'] = 85
    heuristics['Karsaz'] = 99

    
    # Print Graph Nodes
    
    print("=================================================================")
    print("            \n\t\t\tOPTIMAL PATH\n      ")
    print("=================================================================")
    
    print("Bykea Driver destintion Jinnah Airport")
    print("--------------------------------\n\n")
    
    print("Kilometer \t Area")
    print("--------------------------------\n\n")
    graph.printgraph()
  
    # Run search algorithm
    print("--------------------------------\n\n")
    src=input("\n\n(Please Be Sure First Letter must Be Capitalize) \nSource: ")
    Dest=input("Destination: ")
    path = A_Star(graph, heuristics, src, Dest)        
    print("\nPATH: " ,end = " ")
    print(path)
    

# run main method
if __name__ == "__main__": main()


print("Graph")
x = [1,2,3,4,5]
y = [4,6,3,1,2]
plt.plot(x, y)
plt.xlabel('x - axis')
plt.ylabel('y - axis')
plt.title('Source To Destination')
plt.show()