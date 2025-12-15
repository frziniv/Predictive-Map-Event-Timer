# Predictive-Map-Event-Timer
# PredictiveMapTimer.py

import time

# Define critical in-game events and their fixed recurrence intervals in seconds
FIXED_EVENT_INTERVALS = {
    "Global_Resource_Spawn": 180,  # Spawns every 3 minutes
    "Mid_Map_Objective_Reset": 300, # Resets every 5 minutes
    "Ultimate_Cooldown_Boost": 600  # Occurs every 10 minutes
}

class MapPredictor:
    def __init__(self, current_game_time_seconds: int):
        self.start_time = time.time() - current_game_time_seconds
    
    def predict_next_occurrence(self, event_name: str) -> float:
        """
        Predicts the next precise time (in seconds from game start) an event will occur.
        
        :param event_name: The name of the recurring event.
        :returns: Predicted time in seconds from game start.
        """
        if event_name not in FIXED_EVENT_INTERVALS:
            return -1.0
            
        interval = FIXED_EVENT_INTERVALS[event_name]
        elapsed_time = time.time() - self.start_time
        
        # Calculate how many intervals have passed
        intervals_passed = int(elapsed_time / interval)
        
        # Predicted time is the start time of the next interval
        next_occurrence_time = (intervals_passed + 1) * interval
        
        return next_occurrence_time

# Example Usage
if __name__ == "__main__":
    # Assume 120 seconds have passed in the game
    predictor = MapPredictor(120) 
    
    # Next Resource Spawn (180 seconds interval)
    resource_time = predictor.predict_next_occurrence("Global_Resource_Spawn")
    print(f"Predicted Global Resource Spawn (from game start): {resource_time:.0f} seconds")
