# AnelMultiThread

import java.util.Random;
import java.util.concurrent.BlockingQueue;
import java.util.concurrent.LinkedBlockingQueue;
import java.util.concurrent.atomic.AtomicInteger;
import java.util.concurrent.atomic.AtomicReference;
import java.util.logging.Level;
import java.util.logging.Logger;
import jdk.nashorn.internal.runtime.regexp.joni.encoding.CharacterType;

public class AnelMultiThread implements Runnable{
    private static AtomicReference<StringBuffer> p = new AtomicReference<>(); 
    static AtomicInteger atomica = new AtomicInteger(0);
    static BlockingQueue<StringBuffer> ThreadFila = new LinkedBlockingQueue<>();
    private int id;
    
   
    public AnelMultiThread(int id){
        this.id = id;
    }
   
    public int getId(){
        return this.id;
    }
   
    public static StringBuffer fraseAleatoria() {  
        Random random = new Random();
        StringBuffer frase = new StringBuffer();
        
        int index = -1;        
        for(int i = 0; i < random.nextInt((80)+1)+80; i++) {
            frase.append((char)(random.nextInt((122 - 97) + 1) + 97));
        } 
        return frase;
    }
  
    public void run () {      
        try {   
            do {
                if(ThreadFila.peek() != null) {
                    p.set(ThreadFila.take());

                    System.out.println(p);
                    p.get().setCharAt(atomica.get(), (char)(p.get().charAt(atomica.getAndIncrement())-32));
                    ThreadFila.put(p.get());
                    Thread.sleep(1000);                    
                }
            } while(atomica.get() <= p.get().length());
        } catch (StringIndexOutOfBoundsException | InterruptedException se) {
            
        }
    }

    public static void main(String[] args) throws InterruptedException {
        p.set(fraseAleatoria());
        ThreadFila.add(p.get());
        for(int i=0; i<30; i++){   
            Thread t = new Thread(new AnelMultiThread(i));  
            t.setDaemon(true);
            t.start();        
            t.join();
        }
    }
}
