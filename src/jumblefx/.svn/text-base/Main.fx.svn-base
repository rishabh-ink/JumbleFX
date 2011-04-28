/**
 * Open-Source Jumble
 * Created on Jan 7, 2010, 8:18:52 PM by Rishabh Rao
 */

/*
This program is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program.  If not, see <http://www.gnu.org/licenses/>.
 */
package jumblefx;

import java.lang.System;
import javafx.stage.Stage;
import javafx.scene.Scene;
import javafx.scene.input.MouseEvent;
import javafx.scene.input.KeyEvent;
import javafx.scene.control.TextBox;
import javafx.scene.control.Button;
import javafx.scene.text.Text;
import javafx.scene.layout.VBox;
import java.util.Random;
import java.util.Collections;
import java.util.Arrays;
import java.util.List;
import javafx.scene.text.TextAlignment;
import javafx.geometry.HPos;
import javafx.geometry.VPos;
import javafx.scene.text.Font;
import javafx.animation.transition.FadeTransition;
import javafx.animation.Interpolator;
import javafx.data.xml.*;
import javafx.data.pull.*;
import java.io.FileInputStream;
import javafx.scene.shape.Rectangle;
import javafx.scene.paint.Color;
import javafx.scene.paint.LinearGradient;
import javafx.scene.paint.Stop;

/**
 * Holds a words database.
 * @author Rishabh Rao
 */
class WordsRepo {

	/**
	 * Contains a list of available words. By default contains a few words, just in case.
	 * @see #getMaxWords()
	 */
	protected var wordList: String[] = ["NETBEANS", "ECLIPSE"];
	/**
	 * The number of words in the list.
	 */
	protected var wordCount: Integer = wordList.size();
	/**
	 *
	 */
	var wordListXML = PullParser {
			documentType: PullParser.XML;

			onEvent: function(e: Event) {
				if (e.type == PullParser.START_ELEMENT) {
					if (e.qname.name.equalsIgnoreCase("word")) {
						wordList[wordCount++] = e.getAttributeValue(QName { name: "value" });
					}
				}
			}
		}

	postinit {
		try {
			wordListXML.input = new FileInputStream("/home/rishabh/wordList.xml");
			wordListXML.parse();
		} catch (e: java.io.IOException) {
			System.out.println("Oops, looks like we have a problem:\n{e}\nApplication will now run in safe-mode.")
		}
	}

	/**
	 * Returns a new random word from the <code>listOfWords</code> array.
	 * @see #listOfWords
	 */
	function getNewRandomWord(): String {
		var returnString: String = wordList[(new Random()).nextInt(this.getMaxWords())];
		return returnString;
	}

	/**
	 * Returns the maximum number of words present in the <code>listOfWords</code> array.
	 * @see #listOfWords
	 */
	function getMaxWords(): Integer {
		return wordList.size();
	}

}

/**
 * Handles generation and validation of the jumbled words.
 * @author Risabh Rao
 */
class WordMaster {

	/**
	 * Stores the jumbed word, which will be displayed to the user.
	 * @see #correctWord
	 */
	protected var jumbledWord: String;
	/**
	 * Stores the correct word, which is the answer of <code>jumbledWord</code>.
	 * @see #jumbledWord
	 */
	protected var correctWord: String;
	/**
	 * Specified the minimum number of times the jumbling will occur.
	 * @see #MAX_RUN_LENGTH
	 */
	protected def MIN_RUN_LENGTH: Integer = 3;
	/**
	 * Specified the maximum number of times the jumbling will occur.
	 * @see #MIN_RUN_LENGTH
	 */
	protected def MAX_RUN_LENGTH: Integer = 32;
	/**
	 * <code>WordMaster</code> has-a <code>WordsRepo</code>.
	 * Required because of the application's logical structure.
	 */
	protected var techWDB: WordsRepo = new WordsRepo();

	/**
	 * Creates new <code>correctWord</code> and then jumbles it.
	 */
	function refreshJumbler(): Void {
		correctWord = techWDB.getNewRandomWord();
		jumbleUp();
	}

	/**
	 * Shuffles up the word, using <code>java.util.Collections.shuffle()</code> to generate a new jumbled word.
	 * @see java.util.Collections#shuffle()
	 */
	protected function jumbleUp(): Void {
		jumbledWord = correctWord;

		var jumbledArray: Character[] = jumbledWord.toCharArray();
		var jumbledList: List = Arrays.asList(jumbledArray);

		Collections.shuffle(jumbledList);

		// TODO: Find out a better way of converting an ArrayList to String
		jumbledWord = jumbledList.toString();
		jumbledWord = jumbledWord.replaceAll(", ", "");
		jumbledWord = jumbledWord.replace('[', '');
		jumbledWord = jumbledWord.replace(']', '');
	}

	/**
	 * Checks the correct word against the word given by the user.
	 * @see #correctWord
	 */
	function checkAnswer(ans: String): Boolean {
		if (ans.equalsIgnoreCase(this.correctWord)) {
			return true;
		} else {
			return false;
		}
	}

}

/**
 * Handles the animation aspects of the application UI.
 */
class AnimationManager {

	/**
	 * Used by the <code>resultDisplay</code> UI component to fade in and out.
	 * @see JumbleUIManager.#resultDisplay
	 */
	var resultDisplayFader: FadeTransition = FadeTransition {
			duration: 1s
			interpolator: Interpolator.EASEIN;
			fromValue: 0.0
			toValue: 1.0
			autoReverse: true
			repeatCount: 2
		}
}

/**
 * Handles the UI part of the application.
 * @author Rishabh Rao
 */
class JumbleUIManager {

	var anime: AnimationManager = new AnimationManager();
	/**
	 * <code>JumbleUIManager</code> has-a <code>WordMaster</code>.
	 * Required because of the application's logical structure.
	 */
	var jumbleWordMaster = new WordMaster();
	/**
	 * A text label to display the jumbled word on screen.
	 * @see WordMaster#jumbledWord
	 */
	var jumbleTextDisplay: Text = Text {
			textAlignment: TextAlignment.CENTER
			font: Font {
				size: 30
			}
			content: bind jumbleWordMaster.jumbledWord
		}
	/**
	 * A text box to accept user input.
	 */
	var inputTextBox: TextBox = TextBox {
			columns: 20
			onKeyPressed: function(ke: KeyEvent): Void {
				if (ke.code == javafx.scene.input.KeyCode.VK_ENTER) {
					performWordValidation();
				}
			}
		}
	/**
	 * Big title in a fancy font.
	 */
	var titleText: Text = Text {
			fill: LinearGradient {
				startX: 1
				startY: 0
				proportional: true
				stops: [
					Stop { offset: 0.0 color: Color.BLACK; }
					Stop { offset: 1.0 color: Color.BURLYWOOD; }
				]
			}
			content: "Open-Source\nJumble!"
			font: Font {
				name: "LetrasLocas"
				size: 40
			}
			textAlignment: TextAlignment.CENTER
		}
	/**
	 * Author's name.
	 */
	var authorName: Text = Text {
			fill: LinearGradient {
				startX: 1
				startY: 0
				proportional: true
				stops: [
					Stop { offset: 0.0 color: Color.DARKRED; }
					Stop { offset: 1.0 color: Color.ROSYBROWN; }
				]
			}
			content: "Developed by\nRishabh Rao\n(rishabhsrao@gmail.com)"
			font: Font {
				size: 10
			}
			textAlignment: TextAlignment.CENTER
		}

	/**
	 * Verifies with <code>jumbleWordMaster</code> about the correctness of the word.
	 */
	function performWordValidation(): Void {
		if (jumbleWordMaster.checkAnswer(inputTextBox.text)) {
			resultDisplay.content = "Good, you're right!";
			anime.resultDisplayFader.node = resultDisplay;
			anime.resultDisplayFader.playFromStart();
		// startNewGame();
		} else {
			resultDisplay.content = "Oops, you're wrong!";
			anime.resultDisplayFader.node = resultDisplay;
			anime.resultDisplayFader.playFromStart();
		// startNewGame();
		}
	}

	/**
	 * A check button is required to process the <code>WordMaster.checkAnswer()</code>.
	 */
	var checkButton: Button = Button {
			text: "OK"
			onMouseReleased: function(me: MouseEvent): Void {
				performWordValidation();
			}
		}
	/**
	 * Shows the correct word.
	 */
	var showWordButton: Button = Button {
			text: "Show Answer"
			onMouseReleased: function(me: MouseEvent): Void {
				resultDisplay.content = "It's {jumbleWordMaster.correctWord}";
				anime.resultDisplayFader.node = resultDisplay;
				anime.resultDisplayFader.playFromStart();
			}
		}
	/**
	 * A Text label to display the result on screen.
	 */
	var resultDisplay: Text = Text {
			font: Font {
				size: 15
			}
			content: " "
			textAlignment: TextAlignment.CENTER
		}
	// Something similar to a constructor

	postinit {
		// After a new object has been created, and all of its attributes have
		// been assigned their default or explicitly set values,
		// this postinit block (if present) is executed.
		startNewGame();
	}

	/**
	 * Clears everything, fetches a new word and starts again.
	 * @see WordMaster.refreshJumbler()
	 */
	function startNewGame(): Void {
		jumbleWordMaster.refreshJumbler();
	}

	/**
	 * A button which creates a new game.
	 */
	var newButton: Button = Button {
			text: "New Word"

			onMouseReleased: function(e: MouseEvent): Void {
				startNewGame();
				inputTextBox.selectAll();
				inputTextBox.replaceSelection("");
			}
		}
}
/**
 * The main scene object where all the components are stored.
 */
var jumbleScene: Scene = Scene {
		/**
		 * <code>Scene</code> "has-a" <code>JumbleUIManager</code>.
		 * All UI components will get inserted here.
		 */
		var jumbleUI: JumbleUIManager = new JumbleUIManager();

		width: 320
		height: 480
		content: [
			Rectangle {
				width: bind jumbleScene.width
				height: bind jumbleScene.height
				arcHeight: 5.0
				arcWidth: 5.0
				fill: LinearGradient {
					startX: 1
					startY: 0
					proportional: true
					stops: [
						Stop { offset: 0.0 color: Color.WHITE; }
						Stop { offset: 0.34 color: Color.ORANGE; }
						Stop { offset: 0.35 color: Color.LEMONCHIFFON; }
					]
				}

				stroke: Color.DARKGRAY
			}

			VBox {
				width: bind jumbleScene.width
				height: bind jumbleScene.height

				nodeHPos: HPos.CENTER // centers nodes horizontally within the column
				hpos: HPos.CENTER // column will be centered within vbox width
				vpos: VPos.CENTER // rows will be centered within vbox height

				spacing: 15
				content: [
					jumbleUI.titleText,
					jumbleUI.authorName,
					jumbleUI.jumbleTextDisplay,
					jumbleUI.inputTextBox,
					jumbleUI.checkButton,
					jumbleUI.showWordButton,
					jumbleUI.newButton,
					jumbleUI.resultDisplay
				]
			}
		]
	}

/**
 * @author Rishabh Rao
 */
Stage {
	width: 320
	height: 480
	title: "Open-Source Jumble"
	scene: jumbleScene
}
