1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60
61
62
63
64
65
66
67
68
69
70
71
72
73
74
75
76
77
78
79
80
81
82
83
84
85
86
87
88
89
90
91
92
93
94
95
96
97
98
99
100
101
102
103
104
105
106
107
108
109
110
111
112
113
114
115
116
117
118
119
120
121
122
123
124
125
126
127
128
129
130
131
132
133
134
135
136
137
138
139
140
141
142
143
144
145
146
147
148
149
150
151
152
package com.himebaugh.calculator;
 
import java.text.DecimalFormat;
 
import android.annotation.SuppressLint;
import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.view.View.OnClickListener;
import android.view.Window;
import android.view.WindowManager;
import android.widget.Button;
import android.widget.TextView;
 
public class MainActivity extends Activity implements OnClickListener {
 
    private TextView mCalculatorDisplay;
    private Boolean userIsInTheMiddleOfTypingANumber = false;
    private CalculatorBrain mCalculatorBrain;
    private static final String DIGITS = "0123456789.";
 
    DecimalFormat df = new DecimalFormat("@###########");
 
    @SuppressLint("NewApi")
    @Override
    protected void onCreate(Bundle savedInstanceState) {
 
        // hide the window title.
        requestWindowFeature(Window.FEATURE_NO_TITLE);
        // hide the status bar and other OS-level chrome
        getWindow().addFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN);
 
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
 
        mCalculatorBrain = new CalculatorBrain();
        mCalculatorDisplay = (TextView) findViewById(R.id.textView1);
 
        df.setMinimumFractionDigits(0);
        df.setMinimumIntegerDigits(1);
        df.setMaximumIntegerDigits(8);
 
        findViewById(R.id.button0).setOnClickListener(this);
        findViewById(R.id.button1).setOnClickListener(this);
        findViewById(R.id.button2).setOnClickListener(this);
        findViewById(R.id.button3).setOnClickListener(this);
        findViewById(R.id.button4).setOnClickListener(this);
        findViewById(R.id.button5).setOnClickListener(this);
        findViewById(R.id.button6).setOnClickListener(this);
        findViewById(R.id.button7).setOnClickListener(this);
        findViewById(R.id.button8).setOnClickListener(this);
        findViewById(R.id.button9).setOnClickListener(this);
 
        findViewById(R.id.buttonAdd).setOnClickListener(this);
        findViewById(R.id.buttonSubtract).setOnClickListener(this);
        findViewById(R.id.buttonMultiply).setOnClickListener(this);
        findViewById(R.id.buttonDivide).setOnClickListener(this);
        findViewById(R.id.buttonToggleSign).setOnClickListener(this);
        findViewById(R.id.buttonDecimalPoint).setOnClickListener(this);
        findViewById(R.id.buttonEquals).setOnClickListener(this);
        findViewById(R.id.buttonClear).setOnClickListener(this);
        findViewById(R.id.buttonClearMemory).setOnClickListener(this);
        findViewById(R.id.buttonAddToMemory).setOnClickListener(this);
        findViewById(R.id.buttonSubtractFromMemory).setOnClickListener(this);
        findViewById(R.id.buttonRecallMemory).setOnClickListener(this);
 
        // The following buttons only exist in layout-land (Landscape mode) and require extra attention.
        // The messier option is to place the buttons in the regular layout too and set android:visibility="invisible".
        if (findViewById(R.id.buttonSquareRoot) != null) {
            findViewById(R.id.buttonSquareRoot).setOnClickListener(this);
        }
        if (findViewById(R.id.buttonSquared) != null) {
            findViewById(R.id.buttonSquared).setOnClickListener(this);
        }
        if (findViewById(R.id.buttonInvert) != null) {
            findViewById(R.id.buttonInvert).setOnClickListener(this);
        }
        if (findViewById(R.id.buttonSine) != null) {
            findViewById(R.id.buttonSine).setOnClickListener(this);
        }
        if (findViewById(R.id.buttonCosine) != null) {
            findViewById(R.id.buttonCosine).setOnClickListener(this);
        }
        if (findViewById(R.id.buttonTangent) != null) {
            findViewById(R.id.buttonTangent).setOnClickListener(this);
        }
    }
 
    @Override
    public void onClick(View v) {
 
        String buttonPressed = ((Button) v).getText().toString();
 
        if (DIGITS.contains(buttonPressed)) {
 
            // digit was pressed
            if (userIsInTheMiddleOfTypingANumber) {
 
                if (buttonPressed.equals(".") && mCalculatorDisplay.getText().toString().contains(".")) {
                    // ERROR PREVENTION
                    // Eliminate entering multiple decimals
                } else {
                    mCalculatorDisplay.append(buttonPressed);
                }
 
            } else {
 
                if (buttonPressed.equals(".")) {
                    // ERROR PREVENTION
                    // This will avoid error if only the decimal is hit before an operator, by placing a leading zero
                    // before the decimal
                    mCalculatorDisplay.setText(0 + buttonPressed);
                } else {
                    mCalculatorDisplay.setText(buttonPressed);
                }
 
                userIsInTheMiddleOfTypingANumber = true;
            }
 
        } else {
            // operation was pressed
            if (userIsInTheMiddleOfTypingANumber) {
 
                mCalculatorBrain.setOperand(Double.parseDouble(mCalculatorDisplay.getText().toString()));
                userIsInTheMiddleOfTypingANumber = false;
            }
 
            mCalculatorBrain.performOperation(buttonPressed);
            mCalculatorDisplay.setText(df.format(mCalculatorBrain.getResult()));
 
        }
 
    }
 
    @Override
    protected void onSaveInstanceState(Bundle outState) {
        super.onSaveInstanceState(outState);
        // Save variables on screen orientation change
        outState.putDouble("OPERAND", mCalculatorBrain.getResult());
        outState.putDouble("MEMORY", mCalculatorBrain.getMemory());
    }
 
    @Override
    protected void onRestoreInstanceState(Bundle savedInstanceState) {
        super.onRestoreInstanceState(savedInstanceState);
        // Restore variables on screen orientation change
        mCalculatorBrain.setOperand(savedInstanceState.getDouble("OPERAND"));
        mCalculatorBrain.setMemory(savedInstanceState.getDouble("MEMORY"));
        mCalculatorDisplay.setText(df.format(mCalculatorBrain.getResult()));
    }
  
}
CalculatorBrain.java
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60
61
62
63
64
65
66
67
68
69
70
71
72
73
74
75
76
77
78
79
80
81
82
83
84
85
86
87
88
89
90
91
92
93
94
95
96
97
98
99
100
101
102
103
104
105
106
107
108
109
110
111
112
113
114
115
116
117
118
119
120
121
122
123
124
125
126
127
128
129
130
131
132
133
134
135
136
package com.himebaugh.calculator;
 
public class CalculatorBrain {
    // 3 + 6 = 9
    // 3 & 6 are called the operand.
    // The + is called the operator.
    // 9 is the result of the operation.
    private double mOperand;
    private double mWaitingOperand;
    private String mWaitingOperator;
    private double mCalculatorMemory;
 
    // operator types
    public static final String ADD = "+";
    public static final String SUBTRACT = "-";
    public static final String MULTIPLY = "*";
    public static final String DIVIDE = "/";
 
    public static final String CLEAR = "C" ;
    public static final String CLEARMEMORY = "MC";
    public static final String ADDTOMEMORY = "M+";
    public static final String SUBTRACTFROMMEMORY = "M-";
    public static final String RECALLMEMORY = "MR";
    public static final String SQUAREROOT = "√";
    public static final String SQUARED = "x²";
    public static final String INVERT = "1/x";
    public static final String TOGGLESIGN = "+/-";
    public static final String SINE = "sin";
    public static final String COSINE = "cos";
    public static final String TANGENT = "tan";
 
    // public static final String EQUALS = "=";
 
    // constructor
    public CalculatorBrain() {
        // initialize variables upon start
        mOperand = 0;
        mWaitingOperand = 0;
        mWaitingOperator = "";
        mCalculatorMemory = 0;
    }
 
    public void setOperand(double operand) {
        mOperand = operand;
    }
 
    public double getResult() {
        return mOperand;
    }
 
    // used on screen orientation change
    public void setMemory(double calculatorMemory) {
        mCalculatorMemory = calculatorMemory;
    }
 
    // used on screen orientation change
    public double getMemory() {
        return mCalculatorMemory;
    }
 
    public String toString() {
        return Double.toString(mOperand);
    }
 
    protected double performOperation(String operator) {
         
        /*
        * If you are using Java 7, then you can use switch in place of if statements
        *
        *     switch (operator) {
        *     case CLEARMEMORY:
        *         calculatorMemory = 0;
        *         break;
        *     case ADDTOMEMORY:
        *         calculatorMemory = calculatorMemory + operand;
        *         break;
        *     etc...
        *     }
        */
 
        if (operator.equals(CLEAR)) {
            mOperand = 0;
            mWaitingOperator = "";
            mWaitingOperand = 0;
            // mCalculatorMemory = 0;
        } else if (operator.equals(CLEARMEMORY)) {
            mCalculatorMemory = 0;
        } else if (operator.equals(ADDTOMEMORY)) {
            mCalculatorMemory = mCalculatorMemory + mOperand;
        } else if (operator.equals(SUBTRACTFROMMEMORY)) {
            mCalculatorMemory = mCalculatorMemory - mOperand;
        } else if (operator.equals(RECALLMEMORY)) {
            mOperand = mCalculatorMemory;
        } else if (operator.equals(SQUAREROOT)) {
            mOperand = Math.sqrt(mOperand);
 
        } else if (operator.equals(SQUARED)) {
            mOperand = mOperand * mOperand;
 
        } else if (operator.equals(INVERT)) {
            if (mOperand != 0) {
                mOperand = 1 / mOperand;
            }
        } else if (operator.equals(TOGGLESIGN)) {
            mOperand = -mOperand;
        } else if (operator.equals(SINE)) {
            mOperand = Math.sin(Math.toRadians(mOperand)); // Math.toRadians(mOperand) converts result to degrees
        } else if (operator.equals(COSINE)) {
            mOperand = Math.cos(Math.toRadians(mOperand)); // Math.toRadians(mOperand) converts result to degrees
        } else if (operator.equals(TANGENT)) {
            mOperand = Math.tan(Math.toRadians(mOperand)); // Math.toRadians(mOperand) converts result to degrees
        } else {
            performWaitingOperation();
            mWaitingOperator = operator;
            mWaitingOperand = mOperand;
        }
 
        return mOperand;
    }
 
    protected void performWaitingOperation() {
 
        if (mWaitingOperator.equals(ADD)) {
            mOperand = mWaitingOperand + mOperand;
        } else if (mWaitingOperator.equals(SUBTRACT)) {
            mOperand = mWaitingOperand - mOperand;
        } else if (mWaitingOperator.equals(MULTIPLY)) {
            mOperand = mWaitingOperand * mOperand;
        } else if (mWaitingOperator.equals(DIVIDE)) {
            if (mOperand != 0) {
                mOperand = mWaitingOperand / mOperand;
            }
        }
 
    }
}
activity_main.xml  (in res/layout)
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60
61
62
63
64
65
66
67
68
69
70
71
72
73
74
75
76
77
78
79
80
81
82
83
84
85
86
87
88
89
90
91
92
93
94
95
96
97
98
99
100
101
102
103
104
105
106
107
108
109
110
111
112
113
114
115
116
117
118
119
120
121
122
123
124
125
126
127
128
129
130
131
132
133
134
135
136
137
138
139
140
141
142
143
144
145
146
147
148
149
150
151
152
153
154
155
156
157
158
159
160
161
162
163
164
165
166
167
168
169
170
171
172
173
174
175
176
177
178
179
180
181
182
183
184
185
186
187
188
189
190
191
192
193
194
195
196
197
198
199
200
201
202
203
204
205
206
207
208
209
210
211
212
213
214
215
216
217
218
219
220
221
222
223
224
225
226
227
228
229
230
231
232
233
234
235
236
237
238
239
240
241
242
243
244
245
246
247
248
249
250
251
252
253
254
255
256
257
258
259
260
261
262
263
264
265
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/functionPad"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:layout_gravity="center"
    android:orientation="vertical"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin" >
 
    <LinearLayout
        android:id="@+id/row1"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight=".12" >
 
        <TextView
            android:id="@+id/textView1"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:gravity="right"
            android:maxLines="1"
            android:paddingLeft="10dp"
            android:paddingRight="10dp"
            android:text="0"
            android:textAppearance="?android:attr/textAppearanceLarge"
            android:textSize="40sp" />
    </LinearLayout>
 
    <LinearLayout
        android:id="@+id/row2"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight=".12" >
 
        <Button
            android:id="@+id/buttonClearMemory"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight=".25"
            android:text="@string/buttonClearMemory"
            android:textSize="25sp" />
 
        <Button
            android:id="@+id/buttonAddToMemory"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight=".25"
            android:text="@string/buttonAddToMemory"
            android:textSize="25sp" />
 
        <Button
            android:id="@+id/buttonSubtractFromMemory"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight=".25"
            android:text="@string/buttonSubtractFromMemory"
            android:textSize="25sp" />
 
        <Button
            android:id="@+id/buttonRecallMemory"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight=".25"
            android:text="@string/buttonRecallMemory"
            android:textSize="25sp" />
    </LinearLayout>
 
    <LinearLayout
        android:id="@+id/row3"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight=".12" >
 
        <Button
            android:id="@+id/buttonClear"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight=".25"
            android:text="@string/buttonClear"
            android:textSize="25sp" />
 
        <Button
            android:id="@+id/buttonToggleSign"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight=".25"
            android:text="@string/buttonToggleSign"
            android:textSize="25sp" />
 
        <Button
            android:id="@+id/buttonDivide"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight=".25"
            android:text="@string/buttonDivide"
            android:textSize="25sp" />
 
        <Button
            android:id="@+id/buttonMultiply"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight=".25"
            android:text="@string/buttonMultiply"
            android:textSize="25sp" />
    </LinearLayout>
 
    <LinearLayout
        android:id="@+id/row4"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight=".12" >
 
        <Button
            android:id="@+id/button7"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight=".25"
            android:text="@string/button7"
            android:textSize="25sp" />
 
        <Button
            android:id="@+id/button8"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight=".25"
            android:text="@string/button8"
            android:textSize="25sp" />
 
        <Button
            android:id="@+id/button9"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight=".25"
            android:text="@string/button9"
            android:textSize="25sp" />
 
        <Button
            android:id="@+id/buttonSubtract"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight=".25"
            android:text="@string/buttonSubtract"
            android:textSize="25sp" />
    </LinearLayout>
 
    <LinearLayout
        android:id="@+id/row5"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight=".12" >
 
        <Button
            android:id="@+id/button4"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight=".25"
            android:text="@string/button4"
            android:textSize="25sp" />
 
        <Button
            android:id="@+id/button5"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight=".25"
            android:text="@string/button5"
            android:textSize="25sp" />
 
        <Button
            android:id="@+id/button6"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight=".25"
            android:text="@string/button6"
            android:textSize="25sp" />
 
        <Button
            android:id="@+id/buttonAdd"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight=".25"
            android:text="@string/buttonAdd"
            android:textSize="25sp" />
    </LinearLayout>
 
    <LinearLayout
        android:id="@+id/row6"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight=".24"
        android:baselineAligned="false" >
 
        <LinearLayout
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight=".75"
            android:orientation="vertical" >
 
            <LinearLayout
                android:id="@+id/linearLayout1"
                android:layout_width="match_parent"
                android:layout_height="0dp"
                android:layout_weight=".50"
                android:textSize="25sp" >
 
                <Button
                    android:id="@+id/button1"
                    android:layout_width="0dp"
                    android:layout_height="match_parent"
                    android:layout_weight=".33"
                    android:text="@string/button1"
                    android:textSize="25sp" />
 
                <Button
                    android:id="@+id/button2"
                    android:layout_width="0dp"
                    android:layout_height="match_parent"
                    android:layout_weight=".33"
                    android:text="@string/button2"
                    android:textSize="25sp" />
 
                <Button
                    android:id="@+id/button3"
                    android:layout_width="0dp"
                    android:layout_height="match_parent"
                    android:layout_weight=".34"
                    android:text="@string/button3"
                    android:textSize="25sp" />
            </LinearLayout>
 
            <LinearLayout
                android:id="@+id/linearLayout2"
                android:layout_width="match_parent"
                android:layout_height="0dp"
                android:layout_weight=".50" >
 
                <Button
                    android:id="@+id/button0"
                    android:layout_width="0dp"
                    android:layout_height="match_parent"
                    android:layout_weight=".66"
                    android:text="@string/button0"
                    android:textSize="25sp" />
 
                <Button
                    android:id="@+id/buttonDecimalPoint"
                    android:layout_width="0dp"
                    android:layout_height="match_parent"
                    android:layout_weight=".34"
                    android:text="@string/buttonDecimalPoint"
                    android:textSize="25sp" />
            </LinearLayout>
        </LinearLayout>
 
        <Button
            android:id="@+id/buttonEquals"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight=".25"
            android:text="@string/buttonEquals"
            android:textSize="25sp" />
    </LinearLayout>
 
</LinearLayout>
activity_main.xml  (in res/layout-land)
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60
61
62
63
64
65
66
67
68
69
70
71
72
73
74
75
76
77
78
79
80
81
82
83
84
85
86
87
88
89
90
91
92
93
94
95
96
97
98
99
100
101
102
103
104
105
106
107
108
109
110
111
112
113
114
115
116
117
118
119
120
121
122
123
124
125
126
127
128
129
130
131
132
133
134
135
136
137
138
139
140
141
142
143
144
145
146
147
148
149
150
151
152
153
154
155
156
157
158
159
160
161
162
163
164
165
166
167
168
169
170
171
172
173
174
175
176
177
178
179
180
181
182
183
184
185
186
187
188
189
190
191
192
193
194
195
196
197
198
199
200
201
202
203
204
205
206
207
208
209
210
211
212
213
214
215
216
217
218
219
220
221
222
223
224
225
226
227
228
229
230
231
232
233
234
235
236
237
238
239
240
241
242
243
244
245
246
247
248
249
250
251
252
253
254
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/functionPad"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:layout_gravity="center"
    android:orientation="vertical"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin" >
 
    <LinearLayout
        android:id="@+id/row1"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight=".16" >
 
        <TextView
            android:id="@+id/textView1"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:gravity="right"
            android:paddingLeft="10dp"
            android:paddingRight="10dp"
            android:text="0"
            android:textAppearance="?android:attr/textAppearanceLarge"
            android:textSize="30sp" />
    </LinearLayout>
 
    <LinearLayout
        android:id="@+id/row2"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight=".16" >
 
        <Button
            android:id="@+id/buttonClearMemory"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight=".142"
            android:text="@string/buttonClearMemory" />
 
        <Button
            android:id="@+id/buttonSquareRoot"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight=".142"
            android:text="@string/buttonSquareRoot" />
 
        <Button
            android:id="@+id/button7"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight=".142"
            android:text="@string/button7" />
 
        <Button
            android:id="@+id/button8"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight=".142"
            android:text="@string/button8" />
 
        <Button
            android:id="@+id/button9"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight=".142"
            android:text="@string/button9" />
 
        <Button
            android:id="@+id/buttonToggleSign"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight=".142"
            android:text="@string/buttonToggleSign" />
 
        <Button
            android:id="@+id/buttonClear"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight=".142"
            android:text="@string/buttonClear" />
    </LinearLayout>
 
    <LinearLayout
        android:id="@+id/row3"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight=".16" >
 
        <Button
            android:id="@+id/buttonRecallMemory"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight=".142"
            android:text="@string/buttonRecallMemory" />
 
        <Button
            android:id="@+id/buttonSquared"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight=".142"
            android:text="@string/buttonSquared" />
 
        <Button
            android:id="@+id/button4"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight=".142"
            android:text="@string/button4" />
 
        <Button
            android:id="@+id/button5"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight=".142"
            android:text="@string/button5" />
 
        <Button
            android:id="@+id/button6"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight=".142"
            android:text="@string/button6" />
 
        <Button
            android:id="@+id/buttonMultiply"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight=".142"
            android:text="@string/buttonMultiply" />
 
        <Button
            android:id="@+id/buttonDivide"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight=".142"
            android:text="@string/buttonDivide" />
    </LinearLayout>
 
    <LinearLayout
        android:id="@+id/row4"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight=".16" >
 
        <Button
            android:id="@+id/buttonAddToMemory"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight=".142"
            android:text="@string/buttonAddToMemory" />
 
        <Button
            android:id="@+id/buttonInvert"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight=".142"
            android:text="@string/buttonInvert" />
 
        <Button
            android:id="@+id/button1"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight=".142"
            android:text="@string/button1" />
 
        <Button
            android:id="@+id/button2"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight=".142"
            android:text="@string/button2" />
 
        <Button
            android:id="@+id/button3"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight=".142"
            android:text="@string/button3" />
 
        <Button
            android:id="@+id/buttonAdd"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight=".142"
            android:text="@string/buttonAdd" />
 
        <Button
            android:id="@+id/buttonSubtract"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight=".142"
            android:text="@string/buttonSubtract" />
    </LinearLayout>
 
    <LinearLayout
        android:id="@+id/row5"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight=".16" >
 
        <Button
            android:id="@+id/buttonSubtractFromMemory"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight=".142"
            android:text="@string/buttonSubtractFromMemory" />
 
        <Button
            android:id="@+id/buttonSine"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight=".142"
            android:text="@string/buttonSine" />
 
        <Button
            android:id="@+id/buttonCosine"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight=".142"
            android:text="@string/buttonCosine" />
 
        <Button
            android:id="@+id/buttonTangent"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight=".142"
            android:text="@string/buttonTangent" />
 
        <Button
            android:id="@+id/button0"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight=".142"
            android:text="@string/button0" />
 
        <Button
            android:id="@+id/buttonDecimalPoint"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight=".142"
            android:text="@string/buttonDecimalPoint" />
 
        <Button
            android:id="@+id/buttonEquals"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight=".142"
            android:text="@string/buttonEquals" />
    </LinearLayout>
 
</LinearLayout>
strings.xml  (in res/values)
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
<?xml version="1.0" encoding="utf-8"?>
<resources>
 
    <string name="app_name">Calculator</string>
    <string name="menu_settings">Settings</string>
    <string name="action_settings">Settings</string>
    <string name="button0">0</string>
    <string name="button1">1</string>
    <string name="button2">2</string>
    <string name="button3">3</string>
    <string name="button4">4</string>
    <string name="button5">5</string>
    <string name="button6">6</string>
    <string name="button7">7</string>
    <string name="button8">8</string>
    <string name="button9">9</string>
    <string name="buttonAdd">+</string>
    <string name="buttonSubtract">-</string>
    <string name="buttonMultiply">*</string>
    <string name="buttonDivide">/</string>
    <string name="buttonToggleSign">+/-</string>
    <string name="buttonDecimalPoint">.</string>
    <string name="buttonEquals">=</string>
    <string name="buttonClear">C</string>
    <string name="buttonClearMemory">MC</string>
    <string name="buttonAddToMemory">M+</string>
    <string name="buttonSubtractFromMemory">M-</string>
    <string name="buttonRecallMemory">MR</string>
    <string name="buttonSquareRoot">√</string>
    <string name="buttonSquared">x²</string>
    <string name="buttonInvert">1/x</string>
    <string name="buttonSine">sin</string>
    <string name="buttonCosine">cos</string>
    <string name="buttonTangent">tan</string>
 
</resources>
