# movie-booking-project
  1  package.json 
@@ -6,6 +6,7 @@
    "node-sass-chokidar": "0.0.3",
    "npm-run-all": "^4.1.1",
    "react": "^15.6.1",
    "react-addons-css-transition-group": "^15.6.0",
    "react-dom": "^15.6.1",
    "react-scripts": "1.0.13"
  },
  3  src/components/Movie/Movie.css 
@@ -8,3 +8,6 @@
    background: rgba(0, 0, 0, 0.2); }
  .Movie > .details > .metadata {
    padding-left: 10px; }

.Movie > .day-picker .DayPicker {
  overflow: hidden; }
  54  src/components/Movie/Movie.js 
@@ -1,8 +1,21 @@
import React, { Component } from 'react';
import ReactCSSTransitionGroup from 'react-addons-css-transition-group'
import DayPicker from '../../components/DayPicker/DayPicker';
import './Movie.css';

export default class Movie extends Component {
  componentDidUpdate() {
    if(this.props.movie.isActive) {
      this.animateDayPickerSize();
    }
  }

  componentWillUpdate() {
    if(!this.props.movie.isActive) {
      this.animateDayPickerSize();
    }
  }

  render() {
    return (
      <div className="Movie">
@@ -16,10 +29,47 @@ export default class Movie extends Component {
          </div>
        </div>

        <div className="day-picker">
          {this.props.movie.isActive && <DayPicker />}
        <div className="day-picker" ref={(el) => {this.dayPicker = el}}>
          <ReactCSSTransitionGroup transitionName="NoCssTransition" transitionLeave={true} transitionLeaveTimeout={400} transitionEnter={false}>
            {this.props.movie.isActive &&
              <DayPicker />
            }
          </ReactCSSTransitionGroup>
        </div>
      </div>
    )
  }

  animateDayPickerSize() {
    var height,
        d = this.dayPicker.querySelector('.DayPicker'),
        targetheight,
        currentHeight;

    if(d) {
      d.removeAttribute('style');
    }

    height = this.dayPicker.getBoundingClientRect().height

    if(this.props.movie.isActive) { // Appear Animation
      currentHeight = 0;
      targetheight = height;
    }
    else { // Disappear Animation
      currentHeight = height;
      targetheight = 0;
    }

    if(d) {
      d.style.height = currentHeight+'px';
      d.style.transition = 'height 0.4s ease-out';
    }

    requestAnimationFrame( () => {
      if(d) {
        d.style.height = targetheight+'px';
      }
    });
  }
}
  3  src/components/Movie/Movie.scss 
@@ -17,5 +17,8 @@
  }

  >.day-picker {
    .DayPicker {
      overflow: hidden;
    }
  }
}
  6  src/components/Movies/Movies.js 
@@ -41,13 +41,13 @@ export default class Movies extends Component {

    movies.map((movie, i) => {
      if(i === index) {
        movie.isActive = !!!movie.isActive;
        return movie.isActive = !!!movie.isActive;
      }
      else{
        movie.isActive = false;
        return movie.isActive = false;
      }
    })

    this.setState({movies: movies});
    return this.setState({movies: movies});
  }
}
