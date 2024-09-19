# Taller 1

https://github.com/miguel-tellez/Taller1-ARSW.git

![image](https://github.com/miguel-tellez/Taller1-ARSW/assets/77862071/54aff6c0-e738-4053-a222-ebf73c1a33f9)

/*
 * To change this license header, choose License Headers in Project Properties.
 * To change this template file, choose Tools | Templates
 * and open the template in the editor.
 */
package edu.eci.arsw.math;

import java.util.Arrays;

/**
 *
 * @author hcadavid
 * @author Daniel Santanilla
 */
public class Main {

    public static void main(String a[]) {
        System.out.println(bytesToHex(PiDigits.getDigits(0, 10)));
        System.out.println(bytesToHex(PiDigits.getDigits(1, 100)));
        System.out.println(bytesToHex(PiDigits.getDigits(1, 1000000)));
    }

    private final static char[] hexArray = "0123456789ABCDEF".toCharArray();

    public static String bytesToHex(byte[] bytes) {
        char[] hexChars = new char[bytes.length * 2];
        for (int j = 0; j < bytes.length; j++) {
            int v = bytes[j] & 0xFF;
            hexChars[j * 2] = hexArray[v >>> 4];
            hexChars[j * 2 + 1] = hexArray[v & 0x0F];
        }
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < hexChars.length; i = i + 2) {
            // sb.append(hexChars[i]);
            sb.append(hexChars[i + 1]);
        }
        return sb.toString();
    }

}

![image](https://github.com/miguel-tellez/Taller1-ARSW/assets/77862071/0767e170-c7ee-4483-b667-d113094a5a6a)

![image](https://github.com/miguel-tellez/Taller1-ARSW/assets/77862071/e7280cc8-08c3-499d-bb19-04c8cfab2df8)

package

![image](https://github.com/miguel-tellez/Taller1-ARSW/assets/77862071/c1dc0e03-aa0e-4e13-8f81-6274123b9fcf)

package edu.eci.arsw.math;

import java.util.LinkedList;

/**
 * An implementation of the Bailey-Borwein-Plouffe formula for calculating hexadecimal
 * digits of pi.
 * https://en.wikipedia.org/wiki/Bailey%E2%80%93Borwein%E2%80%93Plouffe_formula
 * *** Translated from C# code: https://github.com/mmoroney/DigitsOfPi ***
 * 
 */
public class PiDigits {

    
    private static LinkedList<PiDigitsThread> threads = new LinkedList<>();
    private static int DIGITSPERSUM = 8;
    
    /**
     * Returns a range of hexadecimal digits of pi.
     * 
     * @param start The starting location of the range.
     * @param count The number of digits to return
     * @param N The number of threads to use.
     * @return An array containing the hexadecimal digits.
     */
    public static byte[] getDigits(int start, int count, int N) {
        
        Object lockObject = new Object();
        LinkedList<Integer> digits = new LinkedList<>();

        checkInvalidInterval(start, count);
        createThreads(start, count, N, lockObject);
        for (PiDigitsThread thread : threads) {
            try {
                thread.join();
                digits.addAll(thread.getDigits());
            } catch (InterruptedException ex) {
                ex.printStackTrace();
            }
        }
        byte[] digitsArray = linkedListTobyteArray(count, digits);
        return digitsArray;
    }

    

    private static void checkInvalidInterval(int start, int count) {
        if (start < 0 || count < 0) {
            throw new RuntimeException("Invalid Interval");
        }
    }

    /**
     * Creates threads
     * 
     * @param N Number of threads to create
     */
    private static void createThreads(int start, int count, int N, Object lockObject) {
        int digitsPerThread = count / N;
        for (int i = 0; i < N; i++) {
            int startDigit = i * DIGITSPERSUM;
            int startInterval = i * digitsPerThread;
            int endInterval = (i == N-1) ? count : start + digitsPerThread;
            PiDigitsThread threadI = new PiDigitsThread(startDigit, startInterval, endInterval, i, lockObject);
            threads.add(threadI);
            threadI.start();
        }
    }

    public static byte[] linkedListTobyteArray(int count, LinkedList<Byte> digits) {
        byte[] bytes = new byte[count];
        int i = 0;
        for (Byte b : digits) {
            bytes[i++] = b;
        }
        return bytes;
    }

    

}


![image](https://github.com/miguel-tellez/Taller1-ARSW/assets/77862071/c4d9ce8d-cd1a-4585-ab00-bddd0353ad67)


![image](https://github.com/miguel-tellez/Taller1-ARSW/assets/77862071/0b25b359-0e3c-4505-81f1-c065e2ca6173)


mvn javadoc:javadoc 
mvn javadoc:jar 
mvn javadoc:aggregate 
mvn javadoc:aggregate-jar 
mvn javadoc:test-javadoc 
mvn javadoc:test-jar 
mvn javadoc:test-aggregate 
mvn javadoc:test-aggregate-jar
![image](https://github.com/miguel-tellez/Taller1-ARSW/assets/77862071/dacd0b7e-cf21-424e-af57-2ac182103475)

git pom.xml
git status

![image](https://github.com/miguel-tellez/Taller1-ARSW/assets/77862071/4445097a-dc23-44ef-9a6a-eb3b6b4a1f75)

![image](https://github.com/miguel-tellez/Taller1-ARSW/assets/77862071/58947b8f-c6e6-4754-8f6e-012af65ca20a)

package edu.eci.arsw.math;

import java.util.LinkedList;

public class PiDigitsThread extends Thread {

    private static int DIGITSPERSUM = 8;
    private static double EPSILON = 1e-17;
    private int startDigit, startInterval, endInterval, name;
    private boolean execution;
    private Object lockObject;
    LinkedList<Byte> digits = new LinkedList<>();

    public PiDigitsThread(int startDigit, int startInterval, int endInterval, int name, Object lockObject) {

        this.startDigit = startDigit;
        this.startInterval = startInterval;
        this.endInterval = endInterval;
        this.name = name;
        this.execution = true;
        this.lockObject = lockObject;
    }

    @Override
    public void run() {
        double sum = 0;
        for (int i = startInterval; i < endInterval; i++) {
            if (i % DIGITSPERSUM == 0) {
                sum = 4 * sum(1, startDigit) - 2 * sum(4, startDigit) - sum(5, startDigit) - sum(6, startDigit);
                startDigit += DIGITSPERSUM;
            }
    
            sum = 16 * (sum - Math.floor(sum));
            digits.add((byte) sum);
        }
    }

    /**
     * Returns the sum of 16^(n - k)/(8 * k + m) from 0 to k.
     * 
     * @param m
     * @param n
     * @return
     */
    private static double sum(int m, int n) {
        double sum = 0;
        int d = m;
        int power = n;

        while (true) {
            double term;

            if (power > 0) {
                term = (double) hexExponentModulo(power, d) / d;
            } else {
                term = Math.pow(16, power) / d;
                if (term < EPSILON) {
                    break;
                }
            }

            sum += term;
            power--;
            d += 8;
        }

        return sum;
    }

    /**
     * Return 16^p mod m.
     * @param p
     * @param m
     * @return
     */
    private static int hexExponentModulo(int p, int m) {
        int power = 1;
        while (power * 2 <= p) {
            power *= 2;
        }

        int result = 1;

        while (power > 0) {
            if (p >= power) {
                result *= 16;
                result %= m;
                p -= power;
            }

            power /= 2;

            if (power > 0) {
                result *= result;
                result %= m;
            }
        }

        return result;
    }


    public LinkedList<Byte> getDigits() {
        return digits;
    }

    public void setExecution(boolean execution) {
        this.execution = execution;
    }

    


    
}

![image](https://github.com/miguel-tellez/Taller1-ARSW/assets/77862071/207944ff-54b9-4c05-9ce0-957f1c73fcf2)

![image](https://github.com/miguel-tellez/Taller1-ARSW/assets/77862071/e3f5ff4b-4d64-49fa-981d-53f781d9a90d)

