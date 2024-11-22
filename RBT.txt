export class NodeRBT {
    private data: number;
    private father!: NodeRBT;
    private leftChild!: NodeRBT;
    private rightChild!: NodeRBT;
    private color: string;

    constructor(data: number, isLeaf?: boolean) {
        this.data = data;
        this.color = "RED";
        if (isLeaf) this.color = "BLACK";
    }

    public getData(): number {
        return this.data;
    }

    public setFather(newFather: NodeRBT): void {
        this.father = newFather;
    }

    public getFather(): NodeRBT {
        return this.father;
    }

    public setLeftChild(newChild: NodeRBT): void {
        this.leftChild = newChild;
    }

    public getLeftChild(): NodeRBT {
        return this.leftChild;
    }

    public setRightChild(newChild: NodeRBT): void {
        this.rightChild = newChild;
    }

    public getRightChild(): NodeRBT {
        return this.rightChild;
    }

    public setNodeAsRed(): void {
        this.color = "RED";
    }

    public setNodeAsBlack(): void {
        this.color = "BLACK";
    }

    public getColor(): string {
        return this.color;
    }
}

export class RBTree {
    private root: NodeRBT;
    private leaf: NodeRBT;

    constructor() {
        this.leaf = new NodeRBT(0, true);
        this.root = this.leaf;
    }

    //INSERTAR UN NODO EN EL ARBOL
    public insert(data: number): void {
        let newNode: NodeRBT = new NodeRBT(data);
        let parent: NodeRBT = this.leaf;
        let current: NodeRBT = this.root;

        newNode.setLeftChild(this.leaf);
        newNode.setRightChild(this.leaf);

        while (current !== this.leaf) {
            parent = current;
            if (newNode.getData() < current.getData()) {
                current = current.getLeftChild();
            } else {
                current = current.getRightChild();
            }
        }

        newNode.setFather(parent);

        if (parent === this.leaf) {
            this.root = newNode;
        } else if (newNode.getData() < parent.getData()) {
            parent.setLeftChild(newNode);
        } else {
            parent.setRightChild(newNode);
        }

        if (newNode.getFather() === this.leaf) {
            newNode.setNodeAsBlack();
            return;
        }

        if (newNode.getFather().getFather() === this.leaf) {
            return;
        }

        this.fixInsert(newNode);
    }

    //ELIMINACION
    public delete(data: number): void {
        let nodeToDelete = this.search(data);
        if (!nodeToDelete || nodeToDelete === this.leaf) {
            console.log("Nodo NO encontrado en el Arbol");
            return;
        }
        
        //IMPLEMENTAR EL BALANCEO!!!!!!!!!!!!!!
    }

    //BUSQUEDA
    public search(data: number): NodeRBT | null {
        let current = this.root;
        while (current !== this.leaf) {
            if (data === current.getData()) return current;
            current = data < current.getData() ? current.getLeftChild() : current.getRightChild();
        }
        return null;
    }

    //ROTACION A LA IZQUIERDA
    private leftRotate(x: NodeRBT): void {
        let y: NodeRBT = x.getRightChild();
        x.setRightChild(y.getLeftChild());
        if (y.getLeftChild() !== this.leaf) y.getLeftChild().setFather(x);
        y.setFather(x.getFather());

        if (x.getFather() === this.leaf) this.root = y;
        else if (x === x.getFather().getLeftChild()) x.getFather().setLeftChild(y);
        else x.getFather().setRightChild(y);

        y.setLeftChild(x);
        x.setFather(y);
    }

    //ROTACION A LA DERECHA
    private rightRotate(x: NodeRBT): void {
        let y: NodeRBT = x.getLeftChild();
        x.setLeftChild(y.getRightChild());
        if (y.getRightChild() !== this.leaf) y.getRightChild().setFather(x);
        y.setFather(x.getFather());

        if (x.getFather() === this.leaf) this.root = y;
        else if (x === x.getFather().getRightChild()) x.getFather().setRightChild(y);
        else x.getFather().setLeftChild(y);

        y.setRightChild(x);
        x.setFather(y);
    }

    //ARREGLAR EL ARBOL DESPUES DE INSERTAR UN VALOR
    private fixInsert(testNode: NodeRBT): void {
        while (testNode !== this.root && testNode.getFather().getColor() === "RED") {
            if (testNode.getFather() === testNode.getFather().getFather().getLeftChild()) {
                let uncle: NodeRBT = testNode.getFather().getFather().getRightChild();
                if (uncle.getColor() === "RED") {
                    testNode.getFather().setNodeAsBlack();
                    uncle.setNodeAsBlack();
                    testNode.getFather().getFather().setNodeAsRed();
                    testNode = testNode.getFather().getFather();
                } else {
                    if (testNode === testNode.getFather().getRightChild()) {
                        testNode = testNode.getFather();
                        this.leftRotate(testNode);
                    }
                    testNode.getFather().setNodeAsBlack();
                    testNode.getFather().getFather().setNodeAsRed();
                    this.rightRotate(testNode.getFather().getFather());
                }
            } else {
                let uncle: NodeRBT = testNode.getFather().getFather().getLeftChild();
                if (uncle.getColor() === "RED") {
                    testNode.getFather().setNodeAsBlack();
                    uncle.setNodeAsBlack();
                    testNode.getFather().getFather().setNodeAsRed();
                    testNode = testNode.getFather().getFather();
                } else {
                    if (testNode === testNode.getFather().getLeftChild()) {
                        testNode = testNode.getFather();
                        this.rightRotate(testNode);
                    }
                    testNode.getFather().setNodeAsBlack();
                    testNode.getFather().getFather().setNodeAsRed();
                    this.leftRotate(testNode.getFather().getFather());
                }
            }
        }
        this.root.setNodeAsBlack();
    }

    //METODO IN ORDEN
    private printNode(nodo: NodeRBT): void {
        if (nodo !== this.leaf) {
            this.printNode(nodo.getLeftChild());
            console.log(`${nodo.getData()} (${nodo.getColor()})`);
            this.printNode(nodo.getRightChild());
        }
    }

    //IMPRIMIR TODO EL ARBOL
    public printAll(): void {
        if (this.root === this.leaf) {
            console.log("El Arbol esta Vacio");
        } else {
            this.printNode(this.root);
        }
    }
}

const tree = new RBTree();
tree.insert(10);
tree.insert(20);
tree.insert(30);
tree.insert(15);
tree.insert(25);
tree.insert(5);

console.log("\nARBOL DESPUES DE INSERCCIONES:");
tree.printAll();

const searchValue = 15;
console.log ("\nNODO A BUSCAR:\n",searchValue)
const searchResult = tree.search(searchValue);
if (searchResult) {
    console.log(`Nodo Encontrado: ${searchResult.getData()} (${searchResult.getColor()})`);
} else {
    console.log(`Nodo ${searchValue} NO encontrado en el Arbol`);
}

const deleteValue = 20;
console.log ("\nNODO A ELIMINAR:\n", deleteValue)
console.log(`Eliminando el Nodo con Valor ${deleteValue}...`);
tree.delete(deleteValue);


console.log("\nARBOL DESPUES DE ELIMINAR UN NODO");
tree.printAll();
