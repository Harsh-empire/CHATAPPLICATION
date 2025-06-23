package client;

import java.io.*;
import java.net.*;

public class Chatclient {
    private Socket socket;
    private BufferedReader input;
    private PrintWriter out;
    private BufferedReader userInput;

    public void start(String serverIP, int port) {
        try {
            socket = new Socket(serverIP, port);
            input = new BufferedReader(new InputStreamReader(socket.getInputStream()));
            out = new PrintWriter(socket.getOutputStream(), true);
            userInput = new BufferedReader(new InputStreamReader(System.in));

            System.out.print("Enter your name: ");
            String name = userInput.readLine();
            out.println(name);

            // Receive messages
            new Thread(() -> {
                try {
                    String msg;
                    while ((msg = input.readLine()) != null) {
                        System.out.println(msg);
                    }
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }).start();

            // Send messages
            String msg;
            while ((msg = userInput.readLine()) != null) {
                out.println(msg);
            }

        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {
        Chatclient client = new Chatclient();
        client.start("localhost", 12345); // Replace with IP if remote
    }
}
