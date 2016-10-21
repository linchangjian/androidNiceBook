> #泛型

```
//为什么会诞生泛型，有一个原因，就是解决早期Java用Object来强转方式来接收对象。
public class Main {

    public static void main(String arg[]){

        Holder3<String> holder = new Holder3("T  ni hao ");


        System.out.println(holder.getInstance());

       // holder.setInstance(1);

    }


	//有了泛型以后，很优雅
    static class Holder3<T>{
        private T a;
        public Holder3(T t){
            this.a = t;
        }

        private T getInstance(){
            return this.a;
        }

        private void setInstance(T a){
            this.a = a;
        }
    }
    //没有泛型以前的代码,有泛型了以后,就不需要强转了。
    static class Holder2 {
        private Object obj;

        public Holder2(Object obj) {
            this.obj = obj;
        }

        private Object getInstance() {
            return obj;
        }

        private void setInstance(Object obj) {
            this.obj = obj;
        }
    }
}
```

> ###泛型上的继承

```
//final会对我们的属性进行保护，无法改变值或地址
public class Main {

    public static void main(String arg[]){
        new TwoTuple<Integer,String>(1,"name");
        new ThreeTuple<Integer,String,Integer>(1,"name",2);

    }

    public static class TwoTuple<A,B>{
        public final A first; //public公开了我们的属性,但是final能对我们的属性进行保护
        public final B second;

        public TwoTuple(A a,B b){
            this.first = a;
            this.second = b;
        }
    }

    public static class ThreeTuple<A,B,C> extends TwoTuple<A,B>{
        public final C third;
        public ThreeTuple(A a, B b,C c) {
            super(a, b);
            this.third = c;
        }
    }
}
```
> ####设计一个兼容所有类型的打乱器,类似于彩票的数字打乱

```
import java.util.ArrayList;
import java.util.Random;

/**
 * Created by lcj on 16/10/21.
 */

public class Main {

    public static void main(String arg[]){
        
        RandomList<String> rl = new RandomList<>();

        for (String s : "It's known that he is slow coach".split(" ")) {
            rl.add(s);
        }
        for (int i = 0; i < rl.box.size();i++){
            System.out.printf(rl.select()+" ");
        }

        RandomList<Integer> rlInt = new RandomList<>();
        int [] nums = {112,33,1,23,5,78};
        for (int i : nums) {
            rlInt.add(i);
        }
        System.out.println();
        for (int i = 0; i < rlInt.box.size();i++){

            System.out.printf(rlInt.select()+" ");
        }

    }

    //设计一个兼容所有类型的打乱器,类似于彩票的数字打乱
    public static class RandomList<T>{
        public final ArrayList<T> box = new ArrayList<T>();
        Random random = new Random(100);
        T select(){
         return box.get(random.nextInt(box.size()));
        }
        
        void add(T t){
            box.add(t);
        }
    }
}

```

```
/Library/Java/JavaVirtualMachines/jdk1.8.0_91.jdk/Contents/Home/bin/java
that he is slow known coach he 
33 5 5 112 33 112 
Process finished with exit code 0
```

> ###泛型接口

泛型也可以用于接口上，也是工厂方法设计模式的一种应用。

```
/**
 *这个demo主要是用来生成舞台的演员，有好人，有坏人，就随机生成玩玩泛型
 *
 */
public class Main {

    public static void main(String arg[]) {
        CharacterGenerator gen = new CharacterGenerator();
			//随机生成好人和坏人
        for (int i = 0 ; i < 5 ;i ++){
            System.out.println(gen.next());

        }

      

    }

    interface StoryCharacters<T>{
        T next();

    }

    public static class Character{
        private static int counter = 0;
        private final int id = counter++;

        public String toString(){
            return getClass().getSimpleName() + "  " +id;
        }
    }

    public static class BadGuy extends Character{
    }

    public static class GoodGuy extends Character{

    }

    public static class CharacterGenerator implements StoryCharacters<Character> , Iterator<Character>{

        private Class [] types = {BadGuy.class, GoodGuy.class};
        private static Random r = new Random(47);

        public CharacterGenerator(){

        }

        private int size;
        public CharacterGenerator(int size){
            this.size = size;
        }

        @Override
        public boolean hasNext() {
            return false;
        }

        @Override
        public void remove() {

        }

        @Override
        public Character next() {
            int index = r.nextInt(types.length);

            try {
                 Object obj = types[index].newInstance();
                return (Character)obj;
            } catch (InstantiationException e) {
                e.printStackTrace();
                return null;
            } catch (IllegalAccessException e) {
                e.printStackTrace();
                return null;
            }


        }

//        public class CharactorIterator implements Iterator<Character>{
//
//            int count = size;
//
//            @Override
//            public boolean hasNext() {
//                return count > 0;
//            }
//
//            @Override
//            public Character next() {
//                count--;
//                return CharacterGenerator.this.next();
//            }
//
//            @Override
//            public void remove() {
//
//            }
//        }
//        public Iterator<Character> iterator(){
//            return new CharactorIterator();
//        }

    }

}

```