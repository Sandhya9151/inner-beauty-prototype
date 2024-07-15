# inner-beauty-prototype
python code :
import numpy as np

def extract_measurements(image_path):
    # Simulate measurement extraction
    # In practice, you would use image processing to get these measurements
    # For this example, we'll return random values within a reasonable range
    bust = np.random.uniform(30, 40)  # in inches
    waist = np.random.uniform(20, 30)
    hips = np.random.uniform(30, 45)
    high_hip = np.random.uniform(20, 30)
    return bust, waist, hips, high_hip

def classify_body_shape(bust, waist, hips, high_hip):
    if (abs(bust - hips) <= 1 and abs(hips - bust) < 3.6 and (bust - waist) >= 9) or (hips - waist) >= 10:
        return "Hourglass"
    elif (hips - bust) >= 3.6 and (hips - bust) < 10 and (hips - waist) >= 9 and (high_hip / waist) < 1.193:
        return "Bottom Hourglass"
    elif (bust - hips) > 1 and (bust - hips) < 10 and (bust - waist) >= 9:
        return "Top Hourglass"
    elif (hips - bust) > 2 and (hips - waist) >= 7 and (high_hip / waist) >= 1.193:
        return "Spoon"
    elif (hips - bust) >= 3.6 and (hips - waist) < 9:
        return "Triangle"
    elif (bust - hips) >= 3.6 and (bust - waist) < 9:
        return "Inverted Triangle"
    elif (abs(hips - bust) < 3.6 and abs(bust - hips) < 3.6 and (bust - waist) < 9 and (hips - waist) < 10):
        return "Rectangle"
    else:
        return "Unknown Shape"


        
flask app code (python):

from flask import Flask, request, render_template
import os
from werkzeug.utils import secure_filename

app = Flask(_name_)

UPLOAD_FOLDER = 'uploads'
if not os.path.exists(UPLOAD_FOLDER):
    os.makedirs(UPLOAD_FOLDER)
app.config['UPLOAD_FOLDER'] = UPLOAD_FOLDER

@app.route('/', methods=['GET', 'POST'])
def upload_file():
    if request.method == 'POST':
        file = request.files['file']
        if file:
            filename = secure_filename(file.filename)
            filepath = os.path.join(app.config['UPLOAD_FOLDER'], filename)
            file.save(filepath)
            
            # Extract measurements and classify body shape
            bust, waist, hips, high_hip = extract_measurements(filepath)
            body_shape = classify_body_shape(bust, waist, hips, high_hip)
            
            return render_template('result.html', body_shape=body_shape, bust=bust, waist=waist, hips=hips, high_hip=high_hip)
    return '''
    <!doctype html>
    <title>Upload an Image</title>
    <h1>Upload an Image to Analyze Body Shape</h1>
    <form method=post enctype=multipart/form-data>
      <input type=file name=file>
      <input type=submit value=Upload>
    </form>
    '''

if _name_ == '_main_':
    app.run(debug=True)



HTML code:

<!doctype html>
<html>
<head>
  <title>Body Shape Result</title>
</head>
<body>
  <h1>Predicted Body Shape: {{ body_shape }}</h1>
  <p>Bust: {{ bust }} inches</p>
  <p>Waist: {{ waist }} inches</p>
  <p>Hips: {{ hips }} inches</p>
  <p>High Hip: {{ high_hip }} inches</p>
</body>
</html>
