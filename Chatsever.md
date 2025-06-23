package server;

import java.io.*;
import java.net.*;
import java.util.*;

public class Chatserver {
    private static Set<Clienthandler> Clienthandlers = new HashSet<>();

    public static void main(String[] args) {
        int port = 12345;
        try (ServerSocket serverSocket = new ServerSocket(port)) {
            System.out.println("Server started on port " + port);

            while (true) {
                Socket clientSocket = serverSocket.accept();
                System.out.println("New client connected.");

                Clienthandler handler = new Clienthandler(clientSocket);
                Clienthandlers.add(handler);
                handler.start();
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    static void broadcast(String message, Clienthandler exclude) {
        for (Clienthandler handler : Clienthandlers) {
            if (handler != exclude) {
                handler.sendMessage(message);
            }
        }
    }

    static void removeClient(Clienthandler handler) {
        Clienthandlers.remove(handler);
    }
}
