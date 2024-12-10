# CSS-301

import javafx.application.Application;
import javafx.stage.Stage;
import javafx.scene.Scene;
import javafx.scene.control.Button;
import javafx.scene.control.Label;
import javafx.scene.control.ComboBox;
import javafx.scene.layout.HBox;
import javafx.scene.layout.BorderPane;
import javafx.scene.layout.StackPane;
import javafx.scene.canvas.Canvas;
import javafx.scene.canvas.GraphicsContext;
import javafx.scene.paint.Color;
import javafx.scene.image.Image;
import javafx.scene.input.MouseEvent;
import javafx.geometry.Pos;
import javafx.geometry.Insets;

import java.util.ArrayList;
import java.util.Collections;

/**
 * A modified program that removes a 20-pixel circle centered at mouse clicks
 * to reveal a hidden image.
 */
public class ScratchOffCircle extends Application {

    public static void main(String[] args) {
        launch();
    }
    //-------------------------------------------------------------------

    private final static String[] picFileNames = {
            "bluejay.jpg", "chipmunk.jpg", "collie.jpg", "elephants.jpg", 
            "faun.jpg", "lion.jpg", "polar-bear.jpg", "stork.jpg"
    };

    private Canvas picCanvas;                // canvas where picture is drawn
    private GraphicsContext picGraphics;     // graphics context for picCanvas
    private Canvas overlayCanvas;            // canvas that hides the picture
    private GraphicsContext overlayGraphics; // graphics context for overlayCanvas

    private ArrayList<Image> pictures;       // An arraylist containing the images
    private int nextPicIndex;                // Index of the next picture to be shown

    private final static int width = 800;    // canvas width
    private final static int height = 600;   // canvas height

    private final static double circleRadius = 20.0; // Radius of the circle to be removed

    public void start(Stage stage) {
                
        picCanvas = new Canvas(width, height);
        picGraphics = picCanvas.getGraphicsContext2D();
        overlayCanvas = new Canvas(width, height);
        overlayGraphics = overlayCanvas.getGraphicsContext2D();

        pictures = new ArrayList<>();
        for (String fileName : picFileNames) {
            pictures.add(new Image("nature-images/" + fileName));
        }
        Collections.shuffle(pictures);
        nextPicIndex = picFileNames.length - 1;
        newPic();

        overlayCanvas.setOnMousePressed(this::scratch);
        overlayCanvas.setOnMouseDragged(this::scratch);

        StackPane canvasHolder = new StackPane(picCanvas, overlayCanvas);
        canvasHolder.setStyle("-fx-border-color: #444; -fx-border-width:5px");

        Button newPicBtn = new Button("New Picture!");
        newPicBtn.setOnAction(e -> newPic());
        Button showAllBtn = new Button("Show Entire Picture!");
        showAllBtn.setOnAction(e -> overlayGraphics.clearRect(0, 0, width, height));

        HBox bottom = new HBox(10, newPicBtn, showAllBtn);
        bottom.setAlignment(Pos.CENTER);
        bottom.setPadding(new Insets(10));

        BorderPane root = new BorderPane(canvasHolder);
        root.setBottom(bottom);

        stage.setScene(new Scene(root));
        stage.setResizable(false);
        stage.setTitle("Scratch-Off with Circles");
        stage.show();
    }

    private void newPic() {
        overlayGraphics.setFill(Color.hsb(360 * Math.random(), 0.5, 0.5));
        overlayGraphics.fillRect(0, 0, width, height);
        picGraphics.drawImage(pictures.get(nextPicIndex), 0, 0, width, height);
        if (nextPicIndex == 0) {
            Image firstImage = pictures.get(0);
            do {
                Collections.shuffle(pictures);
            } while (pictures.get(pictures.size() - 1) == firstImage);
            nextPicIndex = pictures.size() - 1;
        } else {
            nextPicIndex--;
        }
    }

    private void scratch(MouseEvent evt) {
        overlayGraphics.clearRect(evt.getX() - circleRadius, evt.getY() - circleRadius, circleRadius * 2, circleRadius * 2);
    }
}



