package server;

import java.io.*;
import java.net.*;

public class Clienthandler extends Thread {
    private Socket socket;
    private PrintWriter out;
    private BufferedReader in;

    public Clienthandler(Socket socket) {
        this.socket = socket;
    }

    public void run() {
        try {
            in = new BufferedReader(new InputStreamReader(socket.getInputStream()));
            out = new PrintWriter(socket.getOutputStream(), true);

            String name = in.readLine();
            System.out.println(name + " joined the chat.");
            Chatserver.broadcast(name + " has joined.", this);

            String msg;
            while ((msg = in.readLine()) != null) {
                Chatserver.broadcast(name + ": " + msg, this);
            }
        } catch (IOException e) {
            System.out.println("Connection lost.");
        } finally {
            Chatserver.removeClient(this);
            try {
                socket.close();
            } catch (IOException e) { }
        }
    }

    void sendMessage(String msg) {
        out.println(msg);
    }
}
