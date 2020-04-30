# montecarlo
package test;
import java.util.*;
import java.util.Random;
import java.util.Arrays;
import java.util.Math;

class MonteCarlo {
  public static void main(String[] args) {
    Scanner console = new Scanner(System.in);

    System.out.print("How many simulations to run: ");
    int simulation = console.nextInt();
    int n = 20; // number of computers in the network
    double p = 0.1; // probability of spreading the virus
    int r = 5; // max number of computers cleaned daily
    int d = 0; // expected number of days until whole network is cleaned
    int v = 0; // number infected after repair
    int totalInfected = 1; // number of computers infected and initially only one is infected

    // Initially computer #0 is infected (set to 1),
    // computers #1 thru #19 are clean (set to 0).
    int[] comps = new int[n];
    comps[0] = 1;
    for (int i=1; i<n; i++) {
      comps[i] = 0;
    }
    System.out.println(Arrays.toString(comps));
    System.out.println();

    while (simulation > 0) {
      while (infectionDetected(comps)) {
        d++;
        // For each wire "infected -> noninfected":
        // perform a Bernoulli trial with probability p=0.1;
        // if success, computer #j becomes newly_infected (set to 2).
        for (int i=0; i<n; i++) {
          for (int j=0; j<n; j++) {
            if ((comps[i] == 1) && (comps[j] == 0)) {
              int x = MyBernoulli(p);
              if (x == 1) {
                comps[j] = 2;
                totalInfected++;
              }
            }
          }
        }
        // After that, mark all newly_infected as simply infected.
        for (int i=0; i<n; i++) {
          if (comps[i] == 2) {
            comps[i] = 1;
          }
        }
        System.out.println(Arrays.toString(comps));
        
        technician(comps, r);
        for (int i=0; i<n; i++) {
          v += comps[i];
        }
        System.out.println(Arrays.toString(comps));
        System.out.println("-------------------------------------------------");
      }
      simulation--;
    }
    System.out.println("Summary:");
    System.out.println("Total Systems Infected: "+ totalInfected);
    System.out.println("Number of days taken to kill infection: "+ d);
    System.out.println("Average number of computers infected per day: "+ (float)totalInfected/d);
    double expected; // expected probabilty each computer gwts infected
    expected = prob(n,p);
    System.out.println("The expected probability each computer gets infected: " +expected);
  }

  private static boolean infectionDetected(int[] array) {
    for (int i = 0; i<array.length; i++) {
      if (array[i] == 1) {
        return true;
      }
    }
    return false;
  }
  private static void technician(int[] array, int x) {
    int fixed = 0;
    for (int i=0; i<array.length && fixed < x; i++) {
      if (array[i] == 1) {
        array[i] = 0;
        fixed++;
      }
    }
  }

  public static int MyBernoulli(double p) {
    double U = Math.random();
    int X;
    if (U < p) {
      X = 1;
    }
	  else {
      X = 0;
    }
    return X;
  }

  public static double prob(int x, double y) {
    double expProb;
    double prob2, prob3, prob4, prob5, prob6;
    prob2 = y/2.0;
    prob3 = (double)(factorial(x)/factorial(0));
    prob4 = (double)Math.pow(prob2,0);
    prob6 = 1.0-prob2;
    prob5 = (double)Math.pow(prob6,x);
    expProb = (double)(1.0 - (prob3*prob4*prob5));
    return expProb;
  }
  public static int factorial(int n) {
    if (n == 0)
      return 1;
    return n*factorial(n-1);
  }
}
