# PredictiveMapTimer.py (Updated Implementation)

import time
import random

# Define critical in-game events with base interval and potential variance
DYNAMIC_EVENT_DATA = {
    "Global_Resource_Spawn": {"base_interval": 180, "variance": 15, "confidence": 0.95}, # Variance of +/- 15s
    "Mid_Map_Objective_Reset": {"base_interval": 300, "variance": 30, "confidence": 0.70},
    "Ultimate_Cooldown_Boost": {"base_interval": 600, "variance": 5, "confidence": 0.99}
}

class MapPredictor:
    def __init__(self, current_game_time_seconds: int):
        self.start_time = time.time() - current_game_time_seconds
    
    def predict_next_occurrence_window(self, event_name: str) -> dict:
        """
        Predicts the next occurrence time window and confidence score.
        
        :param event_name: The name of the recurring event.
        :returns: Dictionary with 'min_time', 'max_time', and 'confidence'.
        """
        if event_name not in DYNAMIC_EVENT_DATA:
            return {"min_time": -1, "max_time": -1, "confidence": 0.0}
            
        data = DYNAMIC_EVENT_DATA[event_name]
        interval = data["base_interval"]
        variance = data["variance"]
        confidence = data["confidence"]

        elapsed_time = time.time() - self.start_time
        
        # Calculate the base time for the next interval
        intervals_passed = int(elapsed_time / interval)
        base_next_time = (intervals_passed + 1) * interval
        
        # NEW: Calculate the prediction window
        min_time = base_next_time - variance
        max_time = base_next_time + variance
        
        return {
            "min_time": min_time, 
            "max_time": max_time, 
            "confidence": confidence
        }

# Example Usage
if __name__ == "__main__":
    # Assume 120 seconds have passed in the game
    predictor = MapPredictor(120) 
    
    # Next Resource Spawn (180 seconds interval, +/- 15s variance)
    resource_prediction = predictor.predict_next_occurrence_window("Global_Resource_Spawn")
    
    print("\n--- Next Global Resource Spawn Prediction ---")
    print(f"Confidence Level: {resource_prediction['confidence']:.2f}")
    print(f"Time Window (Min/Max from game start): {resource_prediction['min_time']:.0f}s to {resource_prediction['max_time']:.0f}s")
    
    # Now, the agent knows it must be positioned for a 30-second window (165s to 195s) with 95% certainty.
