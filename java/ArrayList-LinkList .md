##  ArrayList / LinkList

### Solve

##### 1. 大量数据下linkList前1/10插入效率高，在ArrayList中部,后部插入效率高的原因?

详情看源码：

##### ArrayList

```java
//1.继承AbstractList
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable
{
    //Java的序列化机制是通过在运行时判断类的serialVersionUID来验证版本一致性的
    
    private static final long serialVersionUID = 8683452581122892189L;

   //默认构造的容量为10
    private static final int DEFAULT_CAPACITY = 10;

    /**
     * Shared empty array instance used for empty instances.
     */
     //底层是由一个Object[]数组构成的
    private static final Object[] EMPTY_ELEMENTDATA = {};

```               





##### ArrayList中的随机访问、添加和删除部分源码如下

```java

//获得第index个节点的值
public E get(int index) {  
    rangeCheck(index); //首先判断index的范围是否合法  
  
    return elementData(index);  
}  
  
//将index位置的值设为element，并返回原来的值  
public E set(int index, E element) {  
    rangeCheck(index);  
  
    E oldValue = elementData(index);  
    elementData[index] = element;  
    return oldValue;  
}  
  
//将element添加到ArrayList的指定位置  
public void add(int index, E element) {  
    rangeCheckForAdd(index);  
  
    ensureCapacityInternal(size + 1);  // Increments modCount!!  
    //将index以及index之后的数据复制到index+1的位置往后，即从index开始向后挪了一位  
    System.arraycopy(elementData, index, elementData, index + 1,  
                     size - index);   
    elementData[index] = element; //然后在index处插入element  
    size++;  
}  
  
//删除ArrayList指定位置的元素  
public E remove(int index) {  
    rangeCheck(index);  
  
    modCount++;  
    E oldValue = elementData(index);  
  
    int numMoved = size - index - 1;  
    if (numMoved > 0)  
        //向左挪一位，index位置原来的数据已经被覆盖了  
        System.arraycopy(elementData, index+1, elementData, index,  
                         numMoved);  
    //多出来的最后一位删掉  
    elementData[--size] = null; // clear to let GC do its work  
  
    return oldValue;  
}  
```

#####  LinkedList中的随机访问、添加和删除部分源码如下：

```java

//获得第index个节点的值  
public E get(int index) {  
    checkElementIndex(index);  
    return node(index).item;  
}  
  
//设置第index元素的值  
public E set(int index, E element) {  
    checkElementIndex(index);  
    Node<E> x = node(index);  
    E oldVal = x.item;  
    x.item = element;  
    return oldVal;  
}  
  
//在index个节点之前添加新的节点  
public void add(int index, E element) {  
    checkPositionIndex(index);  
  
    if (index == size)  
        linkLast(element);  
    else  
        linkBefore(element, node(index));  
}  
  
//删除第index个节点  
public E remove(int index) {  
    checkElementIndex(index);  
    return unlink(node(index));  
}  
  
//定位index处的节点  
Node<E> node(int index) {  
    // assert isElementIndex(index);  
    //index<size/2时，从头开始找  
    if (index < (size >> 1)) {  
        Node<E> x = first;  
        for (int i = 0; i < index; i++)  
            x = x.next;  
        return x;  
    } else { //index>=size/2时，从尾开始找  
        Node<E> x = last;  
        for (int i = size - 1; i > index; i--)  
            x = x.prev;  
        return x;  
    }  
}  


```




